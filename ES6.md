# 在学习 Vue 之前，我先了解了这些内容

### ES6 的基础：

- 声明变量：let
- 声明常量：const
- 模板字符串：`${变量/常量名}其他字符串还可换行`
- 解构赋值（包括数组和对象的）
- ...数组扩展运算符`new arr = [...a1,...,a2,其他元素]`
  > 最终数组是 a1 内的元素，加 a2 内的元素，加其他元素一起组成的
- Array.from(伪数组)，将伪数组转为真数组
- 类的定义 `class A{}`
- 类的继承 `class B extends A{}`
- 类的实例化 `b = new B()`
- 箭头函数`let 函数名 = (参数) => {函数体}`，用于替代匿名函数

### ES6 的进阶：

- 异步任务：异步任务会在所有同步任务执行完之后再执行
- Promise 类`new Promise((resolve, reject) =>{})`规范写法
  - {}中如果`resolve(相关参数)`，表示异步任务执行成功，状态为成功
  - {}中如果`reject(相关参数)`，表示异步任务执行失败，状态为失败
- `p1 = new Promise`后，可以`p1.then(箭头函数1表示成功,箭头函数2表示失败)`，根据 Promise 内的结果，即 Promise 的状态被设定为成功，执行函数 1，否则执行函数 2
- `p1.then(成功函数).catch(失败函数)`也可以
- Async：1，要有一个返回值为 Promise 的函数假设为 a，2，在调用 a 的函数 b 前面声明 `async function b(){}`
- 更新: 一个标准的 Promise 类应该这样写

```js
promise = new Promise(asyns (resolve, reject) = > {
    try {
        // 你要执行的函数,前面加 await
        resolve(data) // 返回执行成功后你要得到的数据
    }catch(error) {
        // 返回执行失败后你要得到的信息
        reject(error)
    }
})
```

- 调用它同样应该`try{ await ... }catch(error){ ... }`

---

- Proxy 代理对象

```
// 先定义一个对象
let obj = {
    name: '刘德华',
    age: 19
}
// 再定义一个Proxy代理对象(被代理的对象, {相关函数})
let p1 = new Proxy(obj, { //现在p1就是代理obj的对象
    // 获取值
    get(target, property, receiver) {
        return obj[property]
    },
    // 当代理对象的值发生修改时:
    set(target, property, value, receiver) {
        obj[property] = value //被代理对象的值同步修改
        console.log(obj[property]) //并且打印
    }
    // 还有很多其他函数...,参考文档
})
```

---

- Module 模块

```
// 三,Module模块
// 一个模块可以理解为一个文件
// ESM(浏览器)
/**
 * 1,html script标签声明 type="module"
 * 2,暴露内容 export default { 该模块要暴露的属性,方法... },假设文件为a.js
 * 3,其他文件引用: import xxx from './a.js'
 * 4,如果我是 export 变量名/方法名 进行暴露?
 * 5,那么我需要"解构引用": import{同名变量名,同名方法名,同名as新别名} from './a.js'
 */
// CommenJS(NODEJS)
/**
 * 定义模块module.export={属性,方法}
 * 导入模块let moduleA = require('路径')
 */
```

### TypeScript

- 可以理解为需要编译的，**强类型**的 javascript
  > 需要强类型很简单，a=10 后 a='10'就会报错，易于维护，不会出现'10'+10 = '1010'的画面
- 定义变量时，要么声明类型，要么第一次赋值是什么类型，就是什么类型
  - `let a = 10` //a 从此以后就是 number 类型，再赋值 a = '10' 就报错
  - 或者`let a:number` 从此以后 a 也是 number 类型，只能给赋数值类型的值
- 常见的类型：
  - number 数值
  - string 字符串
  - boolean 布尔
  - null 空
  - undefined 未定义
  - 如果变量 a 是数值但有可能为空？`let a = number | null`
- 数组
  - `let arr:number[] = [1,2,3]` 数值类型数组
  - `let arr:string[] = ['a','b']` 字符串类型数组
  - `let arr:Array<number>` 或者 `let arr:Array<string>`同上
- 元组（个数限定）
  - `let tup:[number, string, number]` 要求元组元素必须是：数值，字符串，数值
  - 无法确定个数时：`let ti:[number,string?] = [1]`不会报错，因为第二个有“？”，但如果要给它值，就必须是声明好的字符串类型
- 枚举
  - `enum MyEnum{A,B,C}`
  - 访问需要`MyEnum.A`
  - 也可以用`MyEnum[0]`
    > A=>0,B=>1,C=>2
- void
  > 类似于 null 或者 undefined，通常用于函数
- 函数
- 参数指定类型`(...形参:类型,形参:类型)`
- 函数指定类型`...():类型{函数体}`

```
function myFn(a:number,b:number):number {
    return a+b
}
```

- 一个更复杂的函数

```
function myFn(a:number, b='haha', c?:boolean, ...rest:number[]):void{
    // ...函数体
}
```

- 拆解

  - 形参 a，数值类型
  - 形参 b，一定是字符串类型，且默认值为'haha'
  - 形参 c，可选参数
  - 形参 d，其余可变参数，是一个由数值作为元素组成的数组
  - 调用这个函数如果传参数(1,'2')，那么 c 没有值也不会报错
  - 如果传参(1,'2',true,1,2,3,4)，那么 rest=[1,2,3,4]
  - 函数的返回值必须为 void，那么这个函数一旦写 return 就会报错

- 接口 interface

  - 定义

  ```
  interface Obj{
      name: string,
      age: number
  }
  ```

  - 调用

  ```
  let obj:Obj = {
      name: '刘德华',
      age: 10
  }
  ```

  > 可以将 Obj 理解为一个规则接口，用:Obj 调用

  > 避免了定义时，输错属性名称，比如 obj:Obj = {'nama': '输错啦！是 name'}

  > 那么会报错

- 类型别名 Type

```
// 定义规则MyuserName 可能是字符串或者数值
type MyUserName = string | number
// 此时a就必须得匹配MyUserName的规则，要么数字，要么字符
let a:MyUserName
```

- 泛型

```
function myFn<T>(a:T, b:T):T[] {
    return [a, b]
}

myFn<number>(1,2,)
myFn<string>('a','b')
```

- 函数名后面的`<T>`代替类型泛型
- 调用时需要`函数名<给T“赋值”>`以声明类型
- 如果 T 是 number，那么后面写的很清楚了，`a:T`，`b:T`,函数返回值:`T[]`，都得是 number
