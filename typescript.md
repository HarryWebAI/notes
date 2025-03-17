# typescript 学习笔记

### 了解 TS

- TS 简而言之就是微软开发的强类型的 JS, 它的代码量大于 JS, 但比 JS 更利于维护, 是 JS 的超集, 更适用于大型项目的开发
- 为何需要 TS? 解决 1 不清不楚的数据类型, 2 有漏洞的逻辑, 3 访问不存在的属性, 4 低级的拼写错误

> ts 是**静态类型检查**, 当出现错误时, 编辑器会直接提示你错误, 而不会等你运行时才报错(代码运行前报错)

### TS 的安装和编译

- TS 需要编译为 JS 才可以运行
- TS 安装命令: `npm install typescript -g`(-g: 全局安装)
- TS 编译命令

```bash
tsc xxx.ts                  # 编译指定的.ts文件

tsc --init                  # 生成编译配置文件(tsconfig.json)
tsc --watch xxx.ts          # 监听指定.ts文件, 当它保存时, 自动编译为.js
tsc --watch                 # 监听当前路径下的所有.ts文件, 当它们保存时, 自动编译为js
```

- 作为学习者, 关于`tsconfig.json`, 我们应该注意

```json
// 指定编译后的js语言的版本 (es2016 => es7, 年份+1)
"target": "ES6",
// 此值默认注销, 但当他值为 true 时, 只有ts文件的语法完全正确, 才会被编译
"noEmitOnError": true,
```

> 一些框架的脚手架工具, 会自动帮助我们编译 ts 文件, 比如 Vue 采用的 Vite, 不需要我们指定, 我们可以直接写 ts, 它自动帮我们编译为 js

### TS 基础语法

```ts
// 定义变量: let 变量名: 数据类型 = 赋值
let a: number = 1;

// 定义函数: function 函数名(变量名1: 变量1的数据类型, 变量名2: 变量2的数据类型): 返回值的数据类型{...}
function test(arg1: number, arg2: string): string {
  //...
}
```

### 额外的知识

###### 大写小写?包装对象

```ts
let str1: string; // 小写
let str2: String; // 大写

str1 = "hello";
str1 = new String("hello"); // 错误! 小string是**基元**, 但大String是**包装对象**

str2 = "hello";
str2 = new String("hello"); // 这个正确, 但对内存不友好, 它的数据类型其实是Object

/** TS建议 number, string, boolean 都使用小写 */

// 包装对象的实际作用
let str = "hello";

str.length; // ? 一个字符串是从何得到.length属性的?
// 实际上它是通过自动装箱实现的
let length = function () {
  // 字符串此时转为了临时的包装对象
  let tempStringObject = new String(str);
  // 再从包装对象中取出属性
  let lengthValue = tempStringObject.length;
  // 再交出去作为.length
  return lengthValue;
};
```

###### 字面量类型

```ts
// b: 定义的类型叫做 hello, 而不是 b = 'hello'
let b: "hello";

// 不过从此以后, b 只能赋值 'hello', 用得不多,了解即可
```

###### 类型推断

```ts
// 定义时直接赋值, 也会限制b的数据类型, 此时typeof b为number
let b = 1;
```

### TS 数据类型

###### JS 有的数据类型

```ts
// js有的数据类型, ts都有
let myString = "123"; // 1 string
let myNumber = 123; // 2 number
let myBoolean = true; // 3 boolean
let myNull = null; // 4 null

let myUndefined = b();
const b = () => {
  console.log(false);
}; // 5函数没有返回值, a为 undefined

let myBigInt = 9007199254740993n; // 6 bigInt

// 7.1引用数据类型_对象
const myObj = {
  name: "刘浩宇",
  age: 30,
};

// 7.2引用数据类型_数组
let myArray = [1, 2, 3];

// 7.3其他内置对象, 包括 Date, RegExp, Map, Set, ...

// 8 Symbol: 唯一且不可变的值，用于避免对象属性冲突
const id1 = Symbol("id");
const id2 = Symbol("id");
console.log(id1 === id2); // false
```

###### TS 多出来的数据类型: any & unknown

- `any`: 任意数据类型, 直接`let a;`, 此时 `typeof a === any`, **任何值都可以赋值给 any**, 并且类型为 any 的变量最后映射到 js 的最终类型是最后一次赋值时, 值的类型
- `unknown`: 未知数据类型, 和 any 一样, 可以被赋任意值, 但和 any 不同, 类型为 unknown 的变量不能直接赋值给其他已约束类型的变量, **相对安全**
- 类型断言: 如果我非要把 unknown 类型的变量赋值给另外一个变量, 就需要类型断言

```ts
let a: unknown;
let b: string;
b = a; // 报错
b = a as string; // 类型断言 a as string
b = <string>a; // 也可以这么写
```

###### TS 多出来的数据类型: ever & void (void 与 Undefined 的区别)

- never 和 void 通常都是用来限制函数返回值类型的
- `never`: 通常由 ts 自行推断(几乎没有人会限制一个变量的返回值是 never, 而是 ts 自己得出结论), never 限制的函数没有返回值, `undefined`都不行, 也就是说**这个函数要么无限递归永不结束, 要么直接抛出异常**
- `void`: 返回值可以被认为是一种特殊的, undefined, 通常一个函数没有`return`, 或者`return;`, 或者`return undefined;` 时, 它的返回值其实就被认为是约束成`void`了
- 但`void`和`undefined`并不完全相同, 函数返回值被限定为`void`时, 就不应该有`let 某个变量 = 某个返回值是void类型的函数`这种语句出现: 因为调用返回值是 void 的函数时, **调用者不应该依赖其返回值做任何操作**(但是我们可以拿真正的 undefined 做文章, 比如参与比较运算时, undefined 可以被视为 false)

###### TS 多出来的数据类型: object & Object

```ts
/**
 * 永远记住一点: object, Object, 都很少用
 * 那我们应该怎么声明对象呢?
 */

/** 声明对象 */
// 声明对象: {属性: 类型; 属性: 类型}
let person: { name: string; age: number };
// 如果还想有其他属性呢? 类似于python可变参数那种 ...; [propName: 类型]: 类型
let person2: { name: string; age: number; [propName: string]: any };
person2 = {
  name: "张三",
  age: 18,
  gender: "男",
  city: "四川",
};

/** 声明函数 */
// 声明函数 (参数:类型, 参数:类型, 可选参数:类型) => 返回值类型, **注意这个 => 和箭头函数没任何关系, 只是ts函数声明的语法
let count: (qwert: number, asdfg: number, c?: number) => number;
count = function (a, b, c) {
  if (c) {
    return a + b + c;
  } else {
    return a + b;
  }
};
// 也可以写成箭头函数
count = (a, b, c) => {
  if (c) {
    return a + b + c;
  } else {
    return a + b;
  }
};
// 和上面一样, 但是第一行已经声明了, 所以下面的写法多余
count = function (a: number, b: number, c?: number): number {
  if (c) {
    return a + b + c;
  } else {
    return a + b;
  }
};

/** 声明数组 */
// 方法1: 类型[]
let arr: number[];
arr = [1, 2, 3];
// 方法2: Array<类型> **注意大写
let arr2: Array<string>;
arr2 = ["a", "b", "c"];
```

###### TS 多出来的数据类型: tuple 元组(特殊数组)

```ts
let myTuple: [string, number];
myTuple = ["hello", 123];
// 元组中元素类型可以不同, 但是**数量**和**类型**必须相同
// 这个"特殊的数组"必须有2个元素, 且第一个必须是string, 第二个必须是number

// 那我实在不确定有多少个元素
let myTuple2: [string, number, ...string[]];
myTuple2 = ["hello", 123, "world", "123", "456", "789"];
// 使用 ...string[] 表示这个元组可以有任意多个string类型的元素
// 但是注意, 类型限制为 string
```

###### TS 多出来的数据类型: enum 枚举

```ts
// 数字枚举
function walk(str: string) {
  if (str === "up") {
    console.log("上");
  } else if (str === "down") {
    console.log("下");
  } else if (str === "left") {
    console.log("左");
  } else if (str === "right") {
    console.log("右");
  }
}

walk("up");
walk("down");
walk("left");
walk("right");

// 上面太容易出错了! 万一我调用时, 写错参数, 函数内部的判断就失效了

enum Direction {
  up,
  down,
  left,
  right,
}

console.log(Direction.up);
console.log(Direction[0]);
// 枚举是一个特殊的对象, 它实际格式是这样的 [up:0, down:1, left:2, right:3, 0: 'up', 1: 'down', 2: 'left', 3: 'right']
function newWalk(str: Direction) {
  if (str === Direction.up) {
    console.log("上");
  } else if (str === Direction.down) {
    console.log("下");
  } else if (str === Direction.left) {
    console.log("左");
  } else if (str === Direction.right) {
    console.log("右");
  }
}
// 枚举可以有效减少代码编写时犯错的几率, 因为调用时必须
newWalk(Direction.up);

// 字符串枚举
enum Direction2 {
  up = "up",
  down = "down",
  left = "left",
  right = "right",
}
// 字符串枚举的值必须手动赋值, 反向映射时, 只能通过枚举名访问
console.log(Direction2.up);

// 常量枚举
const enum Direction3 {
  up = "up",
  down = "down",
  left = "left",
  right = "right",
}
// 常量枚举在编译时会被删除, 所以不能反向映射, js代码非常简短
```

### type

```ts
type shuzi = number;
// 恭喜你创建了一个全新的类型,叫shuzi, 但其实它还是number

// 联合类型: Status类型可以取number或string类型
type Status = number | string;
function printStatus(data: Status): void {
  console.log(data);
}
// 所以我们调用时, 传入的参数data可以是number, 也可以是string类型
printStatus(404);
printStatus("404");

type Gender = "男" | "女";
function printGender(gender: Gender): void {
  console.log(gender);
}
printGender("男");
// printGender('female') // 报错, 因为female不是Gender类型, 必须是'男'或'女'

// 交叉类型: 将多个类型合并成一个类型
type Area = {
  height: number;
  width: number;
};

type Address = {
  city: string;
  street: string;
  number: number;
};
// 交叉类型: 将Area和Address类型合并成一个类型
type House = Area & Address;

// 如果我创建一个house, 它必须同时满足Area和Address类型
const house: House = {
  height: 100,
  width: 100,
  city: "北京",
  street: "朝阳区",
  number: 1001,
};
```

- `联合类型`: 既可以是 a, 也可以是 b
- `交叉类型`: 既要满足 a, 也要满足 b

### class

- 一个常见的类

```ts
/** 封装 */
class Person {
  name: string;
  age: number;

  // 构造函数
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  speack() {
    console.log(`我是 ${this.name}, 我今年 ${this.age} 岁`);
  }
}

const p1 = new Person("张三", 18);
p1.speack;

/** 继承 */
class student extends Person {
  // 新增子类属性
  grade: string;
  constructor(name: string, age: number, grade: string) {
    // 继承父类属性
    super(name, age);
    // 子类属性
    this.grade = grade;
  }
  study() {
    console.log(`${this.name} 在学习`);
  }
  /** 多态 */
  // 重写父类方法
  override speack() {
    console.log(
      `我是学生 ${this.name}, 我今年 ${this.age} 岁, 我在 ${this.grade} 年级学习`
    );
  }
}

const p2 = new student("李四", 20, "三年级");
p2.study();
p2.speack();

/**
 * 属性修饰符
 * public 公共的                => 类内部、子类、外部都可以访问        | 自己, 子类, 外部
 * protected 受保护的           => 类内部、子类可以访问，外部不能访问   | 自己, 子类
 * private 私有的               => 类内部可以访问，子类、外部不能访问   | 仅自己
 * readonly 只读的              => 只读的，不能修改                    | 不可改
 */

/** 简写 */
// 简写构造函数以省略属性定义
class Person2 {
  constructor(public name: string, protected age: number, private id: string) {}
}

/** 抽象类 */
// 抽象类: 抽象类不能被实例化，只能被继承(不能 new 一个抽象类, 可以 extends 一个抽象类)
// 抽象类中可以写抽象方法, 也可以写具体实现, 主要作用就是定义好基础结构之后拿给其他类继承
// 定义一个洗衣机，所有洗衣机都有开关按钮， 可以把洗衣机作为抽象类， 开关按钮作为抽象属性 （当然也可以自己具体实现， 比如规定所有洗衣机都是按压开关形式的）
// 再定义一个海尔牌洗衣机，继承洗衣机， 开关按钮（旋钮的？按键的？具体实现）， 然后海尔洗衣机除了基础功能， 还可以自定义更多功能（属性&方法）
// 海尔牌洗衣机可以被实例化

abstract class WashingMachine {
  // 抽象方法不能有具体实现 没有{}
  abstract startWashing(): void;

  constructor(public button: string) {}
}

class HaierWashingMachine extends WashingMachine {
  startWashing(): void {
    console.log("开始洗衣服");
  }

  constructor(button: string) {
    super(button);
  }
}

const haier = new HaierWashingMachine("旋钮");
haier.startWashing();

abstract class LaundryMachine {
  // 具体实现： 规定按钮(button属性)全部是按压开关式
  button: string = "按压开关";
  abstract startWashing(): void;
}

class HaierLaundryMachine extends LaundryMachine {
  // 这个叫做重写属性， 而不是实现抽象属性， 相当于如果不写， 洗衣机的button默认是'按压开关'
  button: string = "旋钮";
  startWashing(): void {
    console.log("开始洗衣服");
  }
}

/** 说白了, 抽象类就是在 "定义规范" , 主要用于 */
// 1, 定义通用接口(这个接口的形式就是需要有一个属性button, 有一个函数startWashing(), 你想要接上(继承)这个接口, 就必须写上这个按钮, 和这个函数)
// 2, 提供默认实现(button: string = '按压开关', 默认实现, 相当于给出一个默认值)
// 3, 确保子类关键实现(抽象类定义了"空方法", startWashing(), 子类在继承它时必须实现)
// 4, 共享代码和逻辑
```

- 定义类: `class 类名 { 属性..., 方法...}`
- 继承类: `class 子类 extends 父类`
- 子类重写父类的方法: `override 父类定义过的方法名(){}`
- 属性修饰符:

  - `public`: 公开的, 类自己可以访问, 子类可以直接访问, 外面也可以直接访问
  - `protected`: 受保护的, 类自己可以访问, 子类可以直接访问, 外面不可以直接访问
  - `private`: 私有的, 只有类自己可以直接访问, 子类和外面都不能直接访问
  - `readonly`: 只读的, 不可改

  > 这里有个误区, 并不是说受保护的和私有的就不能访问了, 是**不能直接访问**, 那么假如父类自己定义一个公开的函数, `return 私有的或者受保护的属性`, 那子类和外面就可以通过调取这个函数得到具体私有的属性的值

- 定义类时简写, `class 类名 { constructor(属性修饰符 属性名: 属性类型, ...){} }`

- 定义抽象类: `abstract class 类型 {可以有抽象属性, 抽象方法, 也可以有具体属性, 具体方法(可以理解为默认值)}`

### 接口

```ts
// 定义接口就在是定义结构, 主要作用是为类, 对象, 函数等规定一种契约, 确保代码的一致性和类型安全
// **但是接口只能定义格式, 不能有任何实现**(和抽象类不同, 抽象类可以抽象属性和函数, 也可以具体实现)

/** 接口定义类的结构 */
interface PersonInterface {
  name: string;
  age: number;
  speack(n: number): void;
  /**
   * 规定:
   * 得有name, age两个属性
   * 得有speak这个函数的具体实现
   */
}
// 类实现接口 [类 implements 接口 {具体实现}]
class Person1 implements PersonInterface {
  // 具体实现 speak() 函数
  speack(n: number): void {
    for (let i = 0; i < n; i++) {
      console.log(`我叫${this.name}, 我今年${this.age}岁`);
    }
  }

  // 构造函数简写: 实现name , speak两个属性
  constructor(public name: string, public age: number) {}
}

/** 接口定义对象结构 */
interface UserInterface {
  id: number;
  // 只读属性
  readonly name: string;
  // 可选属性
  email?: string;
  // 函数类型
  run(n: number): void;
}
// 对象实现接口 [新对象: 接口 = {具体实现}]
const user: UserInterface = {
  id: 1,
  name: "张三",
  run(n: number): void {
    console.log(`我跑!`);
  },
};
// user.name = '李四' // 报错, 因为name是只读属性

/** 接口定义函数 */
interface CountInterface {
  (a: number, b: number): number;
}
// 函数实现接口 函数: 接口 = {具体实现(箭头函数)}
const countFunction: CountInterface = (a: number, b: number): number => {
  return a + b;
};

/** 接口继承 */
interface PersonInterface {
  name: string;
  age: number;
}
// 继承
interface StudenInterface extends PersonInterface {
  grade: string; //年纪
}

// 接口自动合并(可重复定义)
// 定义一个接口
interface AnimalInterface {
  category: string; //有分类属性
}
// 定义一个同名接口
interface AnimalInterface {
  gender: string; //有性别属性
}
// 会发现实现时, 必须把两个同名接口定义的所有属性都实现
const animal: AnimalInterface = {
  // 不写category或者gender都会报错
  category: "狗",
  gender: "公",
};

/** 何时应该使用接口? */
// 1, 定义对象格式: 描述数据模型, API响应格式, 配置对象等等... **是开发中使用最多的场景**
// 2, 规定一个类需要哪些属性和方法
// 3, 一般用于扩展第三方的类型, 这种特性在大型项目中可能会用到(自动合并)

/** interface 和 type有何区别? */
// 接口可继承, 合并, 更专注于定义对象和类的结构, (我如果想要给接口追加属性或者方法, 可以利用接口可重复定义的特性, 直接再声明一个同名接口)
// type可以定义类型别名, 联合类型(a | b), 交叉类型(a & b), 但不支持自动合并(想要追加属性或者方法, 我得找到先前的type然后交叉&{新的属性和方法})

/** interface 和 抽象类有何区别? */
// 接口只能描述结构, **不能有任何实现**, **一个类可以实现多个接口**
// 但抽象类可以具体实现(可以有抽象方, 也可以有具体方法), **一个类只能继承一个抽象类**
```

- 定义接口: `interface 接口名 { //定义结构 }`
- 类去实现一个接口: `class 类名 implements 接口名 { //根据接口结构实现 }`
- 对象去实现一个接口: `const 对象名: 接口名 = { //根据接口结构实现 }`
- 函数去实现一个接口: `const 函数名: 结构名 = ( //接口规定的参数结构 ): 接口规定的返回值 => { //这个函数要干嘛? }`
- 接口可以继承: `interface 子接口 extends 父接口 { //除了父接口的结构外, 子接口还想新增什么结构? }`
- 接口可以重复定义: `interface 同名接口`, `interface 同名接口`, 这时要实现这个同名接口, 就必须要满足两个接口的结构, 类似`type.交叉结构`
- `interface`, `abstract class`, `type` 的区别? 详见参考代码最后部分

### .d.ts

- 一句话说明白: 有些库是`.js`写的, 为了约束这个`.js`文件中各个变量, 函数返回值的类型, 实现 ts 这样强类型约束的效果, 就需要写一个同名的, 但后缀是`.d.ts`的文件

### TS 装饰器

> 装饰器的本质是一种特殊的**函数**, 它可以对: **类**, **属性**, **方法**, **参数**进行扩展

###### 开启装饰器功能

- 编辑`tsconfig.json`

```json
// 打开实验性装饰器(取消注释)
"experimentalDecorators": true,
```

###### 装饰器的种类

1. 类装饰器
2. 属性装饰器
3. 方法装饰器
4. 访问器装饰器
5. 参数装饰器

###### 类装饰器

```ts
/** 类装饰器 */

// 接口: 声明格式, 提供类本不存在的方方法
interface Person {
  getTime(): void;
  introduce(): void;
}
// 装饰器1: 重写toString方法
function CustomString(target: Function) {
  target.prototype.toString = function () {
    return JSON.stringify(this);
  };
}
// 装饰器2: 替换被装饰的类
type Constructor = new (...args: any[]) => {};
function LogInfo<T extends Constructor>(target: T) {
  return class extends target {
    constructor(...args: any[]) {
      super(...args);
      this.createdTime = new Date();
    }

    createdTime: Date;

    getTime() {
      console.log(`该对象的创建时间是${this.createdTime}`);
    }
  };
}
// 装饰器工厂加工装饰器3
function DecoratorFactory(n: number) {
  return function (target: Function) {
    target.prototype.introduce = function () {
      for (let i = 0; i < n; i++) {
        console.log(`我叫${this.name}, 我今年${this.age}岁`);
      }
    };
  };
}

@CustomString
@LogInfo
@DecoratorFactory(5)
class Person {
  constructor(public name: string, public age: number) {}
}

const p = new Person("张三", 18);
p.getTime();
p.introduce();
console.log(p.toString());
```

- 为什么要定义接口? 通过接口可重复定义的特性, 实现 Person 接口"提前声明"Person 类中的两个不存在的, 由装饰器提供的方法`getTime()`, `introduce()` (否则直接调用会出现报错提示)

1. 第一个装饰器, 实现了 `object.toString()` 函数不会再返回 `[Object object]`, 而是返回 JSON 字符串
2. 第二个装饰器, 实现了替换原本的`Person`类, 返回一个新的`Person`类, 新的类多了一个属性: `createdTime`, 以及一个函数 `getTime()`
3. 第三个装饰器, 最外层的函数是`装饰器工厂`, 它可以使装饰器更加灵活(比如这里就可以实现接收参数), 它的返回值是一个`装饰器`(这才是真正的装饰器, 外层只是为了实现我们对内层装饰器的要求: 要求它能够接收参数 n, 通过 n 的值决定打印多少次)
4. 使用装饰器`@装饰器名`, 使用装饰器工厂再调用装饰器`@装饰器工厂(接收的参数)`
5. 装饰器调用的顺序: 首先调用工厂生成装饰器, 然后依照**从下到上**, 离`class`越近越先调用的顺序实现装饰器对类的装饰

###### 属性装饰器

```ts
// 定义属性装饰器
function State(target: object, propertyKey: string) {
  let key = `__${propertyKey}`;

  Object.defineProperty(target, propertyKey, {
    get() {
      return this[key];
    },
    set(newValue) {
      this[key] = newValue;
      console.log(`${propertyKey}属性被设置为${newValue}`);
    },
  });
}

class Person {
  name: string;
  // 使用装饰器装饰属性
  @State
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

const p1 = new Person("张三", 18);
const p2 = new Person("李四", 20);
p1.age = 30;
p2.age = 40;
```

1. 定义属性装饰器时, 参数列表应该是 `(target: object, propertyKey: string)`, 分别对应: `target: 被监视的实例(如果是静态属性则是类本身)`, `propertyKey属性名`
2. 这个装饰器实现了一个简单的功能: 重写`Object.defineProperty()`函数, 当类(实例对象)被装饰的属性变更时, 打印一句话
3. 但是需要注意, 这个装饰器是加在类.属性上的, 也就是说, 这个装饰器必须做特殊处理, 来区分开不同的实例对象, 所以需要`` let key = `__${propertyKey}`; ``, 再`this[key]`获取(get)和修改(set)

###### 方法装饰器

```ts
function Logger(
  target: object,
  propertyKey: string,
  descriptor: PropertyDescriptor
) {
  // 存储原始方法
  const originMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`${propertyKey}开始执行了...`); // 执行前可以完成更多逻辑...
    const result = originMethod.call(this, ...args);
    console.log(`${propertyKey}执行结束了...`); // 执行后可以完成更多逻辑...

    return result;
  };
}

class Person {
  constructor(public name: string, public age: number) {}

  @Logger
  speak() {
    console.log(`${this.name}说：我今年${this.age}岁`);
  }

  static isAdult(age: number) {
    return age >= 18;
  }
}

const p = new Person("张三", 18);
p.speak();
```

1. 定义了一个装饰器, 它接收参数`(对象/类, 属性名, 属性描述器)`
2. 通过`属性描述器.value`获得当前函数, 存为`原始方法`
3. 重写当前函数, 实现: `1, 在函数正式调用前做些逻辑`, `2.调用原始方法`, `3.在函数正式调用后做些其他的逻辑`
4. 装饰函数`speak()`, 实现 demo: 调用某函数时使其除了做本身的工作外, 还可以实现更加复杂需求和逻辑

###### 访问器装饰器

```ts
function WeatherRangeValidate(min: number, max: number) {
  return function (
    target: object,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    const originSetMethod = descriptor.set;

    descriptor.set = function (value: number) {
      if (value < min || value > max) {
        throw new Error(`温度必须在${min}到${max}之间`);
      }

      if (originSetMethod) {
        originSetMethod.call(this, value);
      }
    };
  };
}

class Weather {
  private _temp: number;

  constructor(temp: number) {
    this._temp = temp;
  }

  get temp() {
    return this._temp;
  }

  @WeatherRangeValidate(0, 100)
  set temp(value: number) {
    this._temp = value;
  }
}

const weather = new Weather(20);
weather.temp = 101;
console.log(weather.temp);
```

1. 定义了一个装饰器工厂, 代装饰器接收参数: 最高温度, 最低温度
2. 装饰器工厂返回一个针对访问器(`set ...`, `get ...`)的装饰器, 通过装饰器工厂接收的参数进行校验
3. 装饰器接收三个参数: `(target:对象/类, 属性名, 属性描述器)`
4. 装饰器做了这几件事: `1, 获取原始set`, `2, 重写set`, `2.1判断范围,如果发生错误直接抛出异常` `2.2如果通过判断则调用原方法`
5. 给访问器 set 加上装饰器, 设置可以传入的范围, 以此实现了简单的数据校验 demo

###### 参数装饰器
- 一句话表述: 定义参数装饰器时, 装饰器接收3个参数 `(1对象/类, 2参数名, 3参数在参数列表中的索引)`

###### 总结
- **装饰器本质上就是个函数**
1. 所有装饰器的第一个参数都是对象或者类(非静态属性或者方法就是对象本身, 是静态属性就是类本身)
2. 除了类装饰器外, 所有装饰器的第二个参数都是`key`, 也就是属性名, 函数名, 访问器(set/get), 参数名
3. 如果装饰器有第三参数(函数, 访问器, 参数), 函数和访问器的第三参数都是`属性描述器`, 可以通过`.函数名/set/get`获取指定函数, 通常实际使用中是: `1获取原函数`, `2重现原函数`, `3实现复杂逻辑逻辑前后调用原函数`(比如验证数据是否合规啊, 在调用原函数前后做些其他什么事啊)
4. 装饰器在装饰任何东西时, 不能加参数`@装饰器`, 没有括号, 如果"想有括号"接收参数? 就必须使用**装饰器工厂**
  - 装饰器工厂也是个函数, 它的返回值是一个装饰器, `return function(...){...}`
  - 但是它能"代不能接收参数的装饰器函数接收参数", 就这么个功能
