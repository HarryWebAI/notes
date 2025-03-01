# nvm, node, vue

### NVM

- Node Version Manage: Node 版本管理器
- <a href="https://github.com/coreybutler/nvm-windows/releases">nvm 下载地址</a>
- 下载后右键以管理员身份打开安装程序
- 配置好 nvm 安装路径,和 nodejs 文件映射路径
- 确认**系统环境变量**配置成功，包括
  - `NVM_HOME`
  - `NVM_SYMLINK`
  - `Path`中的`%上面两项%`

### Node

- Node.js:一个跨平台的 javascript 运行环境(不仅仅在浏览器上运行，还可以在服务端运行)
- 使用 nvm 安装 node `nvm install node`
- 其他 nvm 命令:
  - `nvm install [指定版本号]` => 安装指定版本
  - `nvm uninstall [指定版本号]` => 卸载指定版本
  - `nvm list` => 查看已经安装的版本
  - `nvm use [指定版本号]` => 使用指定版本

# npm 与 Vue

### npm

- Node Package Manage: Node 包管理器
- 安装好 nvm,通过 nvm 安装好 node 后,npm 命令生效
- 使用 npm 全局安装 node 包`npm install [包名] -g`

### Vue

- 一款 Javascript 的开源框架,用于构建用户界面(UI)
- 使用 npm 创建 Vue 项目`npm create vue@latest` => 在当前路径下,创建最新版的 vue 项目,命令执行后,会提示你进行以下配置
  - 输入项目名称
  - 选择是否使用 TypeScript(强类型的 js)
  - 选择是否使用 JSX(一般 react 里面常用)
  - 选择是否引入 VueRouter(Vue 路由功能)
  - 选择是否引入 Pinia 状态管理(用户状态管理功能)
  - 使用是否引入 Vitest 单元测试
  - 选择是否引入一款端到端的测试工具
  - 选择是否引入 ESlint 插件用于代码质量检测(看编辑器装没装)
  - 选择是否引入 Prettier 插件用于代码格式化(看编辑器装没装)
  - 选择是否引入 Vue DevToos 调试工具(建议选择是)
  - 创建完毕.
- 项目创建完成后,想要直接运行,还需要进入项目路径,执行以下 3 条命令:
  - `npm install` => 安装项目依赖的 node 包
  - `npm run format` => 启动格式化代码
  - `npm run dev` => 启动运行环境
- 关于依赖包的声明
  - 声明于`~/package.json`,以下两项配置中(dependencies & devDependencies)
  ```
  "dependencies": {
    "vue": "^3.5.13"
  },
  "devDependencies": {
    "@eslint/js": "^9.18.0",
    "@vitejs/plugin-vue": "^5.2.1",
    "@vue/eslint-config-prettier": "^10.1.0",
    "eslint": "^9.18.0",
    "eslint-plugin-vue": "^9.32.0",
    "prettier": "^3.4.2",
    "vite": "^6.0.11",
    "vite-plugin-vue-devtools": "^7.7.0"
  }
  ```

# Vue 的目录结构

### 目录

- `~/` => 根
  - `node_modules/` => 所有的 node 包
  - `public/` => 公共文件
  - `src/` => 源代码(主要工作区)
    - `assets/` => 资源文件(图片,字体,全局 css 等)
    - `components/` => 组件
    - `App.vue` => 项目入口文件
    - `main.js` => 主入口的 js 文件
  - `.gitattributes` => git 配置文件(二进制文件配置)
  - `.gitignore` => git 配置文件(声明不追踪的文件)
  - `index.html` => 页面入口
  - `package.json` => json 配置(声明依赖包等)
  - `vite.config.js` => vite 插件配置

### 项目载入原理

- `~/index.html`中有一个 id 为 app 的 div 标签,并通过 script 标签引入了 module:`main.js`
- `~/src/App.vue`中写好了 template,style,script 标签和代码
- `~/src/main.js`执行代码,导入了`main.css`,`App.vue`,并且将内容挂载到了 id 为 app 的 div 中

# .vue 文件的结构与组件命名

- 一个典型的**Vue3**+中.vue 文件的结构,以`App.vue`为例,由以下三部分组成

```vue
<script setup>
脚本:可以理解为js...
setup: Vue3语法
</script>

<template>模板:可以理解为html...</template>

<style scoped>
样式:可以理解为css...
scoped: 该style标签内的css只作用于该文件
</style>
```

- `vue3`与旧 vue 最大的区别,就是`组合式api`和选项式 api 的区别,因为我是从 Vue3 开始的,所以选项式 api 了解即可.
- **vue3**最大的问题就是不好给组件指定名称,某些情况需要组件名称与文件名称不同时,需要通过再增加一个 script 标签

```
<script>
export default { name: '组件名称' }
<script>
```

- 或者通过插件`vite-plugin-vue-setup-extend`实现直接在 script 上指定 name 属性`<script name="组件名字">`

  - 安装`npm install vite-plugin-vue-setup-extend --save-dev`
  - 配置`~/vite.config.js`

  ```
  // 导入这个包
  import VueSetupExtend from 'vite-plugin-vue-setup-extend'
  ...
  export default defineConfig({
  // 插件:
  plugins: [
    // 新增这一行代码
    VueSetupExtend(),
  ],
  ...
  })
  ...
  ```

  - 为什么用 npm 执行安装命令,需要加参数`--save-dev`?因为加上此参数后,package.json 中会写入相应依赖,以后别人克隆我们的代码,不需要克隆`~/node_modules`,而是克隆完后直接`npm install`即可安装项目所需的 modules

# 响应式变量

### 通过 ref() 函数定义

- 先新建一个组件`~/components/PersonComponent.vue`
  > 注意命名,不建议直接`Person.vue`,而是使用 vue 的 multi-word 命名规,这个规则要求 Vue 组件的名称必须是多个单词组成的，以避免与现有的或未来的 HTML 元素冲突.
- 编辑该文件

```vue
<script setup name="Peroson">
// ref()定义响应式变量
import { ref } from "vue"; //导入ref

let username = ref("张三"); //定义变量

const onUpdateUsername = () => {
  username.value = "李四"; //在script标签中,修改响应式变量需要 变量名.value = 新值
};

// 也可以定义为一个对象
let person = ref({
  username: "张三",
  age: 18,
});

const onUpdatePersonAge = () => {
  person.value.age = 20; //同样,在script标签中,读取这个对象的属性需要 变量名.value.属性
};
</script>

<template>
  <div>
    <!-- 但在template标签中不需要,直接{{ 变量名 }} -->
    <h1>{{ username }}</h1>
    <button @click="onUpdateUsername">点击修改姓名</button>
  </div>

  <div>
    <h1>peoson的年龄:{{ person.age }}</h1>
    <button @click="onUpdatePersonAge">点击修改年龄</button>
  </div>
</template>

<style scoped></style>
```

- 在 `App.vue` 中导入该组件

```vue
<script setup>
// 导入组件
import Peroson from "./components/PersonComponent.vue";
</script>

<template>
  <!-- 挂载该组件 -->
  <Peroson></Peroson>
</template>

<style scoped></style>
```

> 这里有坑:导入 ref()的时候,需要加花括号{},在 App. 导入组件的时候,**不能加{}**

### 通过 reactive()函数定义

- 新建`~/components/BookComponent.vue`并编辑:

```vue
<script setup name="Book">
import { reactive } from "vue";

// reactive() 不能用来定义基本数据类型的响应式变量
// let username = reactive('张三')

// 只能用于定义复合类型的响应式变量(数组,对象)
let book = reactive({
  name: "红楼梦",
  author: "施耐庵",
});

const onUpdateBookAuthor = () => {
  book.author = "曹雪芹";
};

const onUpdateBookInfo = () => {
  // !注意:不能够用这种方式直接修改整个对象,会导致其不再是响应式
  // book = {
  //   name: '三国演义',
  //   author: '罗贯中',
  // }
  // 而应该使用 Object.assign(变量名, 新值)
  Object.assign(book, { name: "三国演义", author: "罗贯中" });
};
</script>

<template>
  <h1>
    书名{{ book.name }}
    <small>作者:{{ book.author }}</small>
  </h1>
  <button @click="onUpdateBookAuthor">纠正作者</button>
  <button @click="onUpdateBookInfo">修改书本信息</button>
</template>

<style scoped></style>
```

> 可以直接通过 `对象.属性` 进行访问

> 注意修改(重新赋值)同时保持变量为响应式的方法:`Object.assign(变量名, 新值)`

- 导入到组件 `App.vue`,略

### 什么时候用 ref()什么时候用 reactive()?

- 基本数据,必须使用 ref()
- 层级不深的数据,建议 ref()
- 层级很多的数据,比如数组包对象,建议使用 reactive()

# 模板语法

### 一,文本

```vue
<script setup>
import { ref } from "vue";
let username = ref("张三");

const onUpdateUsername = () => {
  username.value = "李四";
  window.alert(`现在的姓名是:${username.value}`);
  // 会发现值实际上变了,但模板上不会变
};
</script>

<template>
  <!-- 当在标签中声明 v-once 后,模板渲染完毕,此值不再重复渲染,也就是表面上不会再变了 -->
  <p v-once>姓名:{{ username }}</p>
  <button @click="onUpdateUsername">点击修改姓名</button>
</template>

<style scoped></style>
```

- 渲染文本`{{}}`
- 给包文本的标签指定`v-once`后,即使改变标签内的文本,也不会再次渲染,即表面上的值不会改变

### 二,原生 html

```vue
<script setup>
import { ref } from "vue";

// 定义一段html代码
let code = ref("<h1>你好,vue</h1>");
</script>

<template>
  <div v-html="code"></div>
</template>

<style scoped></style>
```

- 通过给标签指定`v-html="变量名"`属性,使 html 代码显示在该标签中

### 三,标签绑定属性

```vue
<script setup>
let className = "box";
</script>

<template>
  <div v-bind:class="className">注意颜色</div>
  <!-- 可以简写 -->
  <div :class="className">可以简写</div>
</template>

<style scoped>
.box {
  background-color: aqua;
}
</style>
```

- 通过给标签指定`v-bind:class="变量名"`来给标签绑定所属的 class
- 不能直接写`<标签 class="{{ 变量名 }}">`
- 可以简写为`<标签 :class="变量名">`,即可`v-bind:属性`变为`:属性`

### 四,JavaScript 表达式

```vue
<script setup>
let age = 17;
</script>

<template>
  <!-- 直接在模板中使用js表达式进行判断 -->
  <p>{{ age >= 18 ? "成年" : "未成年" }}</p>
</template>

<style scoped></style>
```

### 五,v-if 条件判断

```vue
<script setup>
let weather = "rainy";
</script>

<template>
  <p v-if="weather == 'sun'">出去玩</p>
  <p v-else-if="weather == 'windy'">风有点大</p>
  <p v-else>天气不好出不去</p>
</template>

<style scoped></style>
```

- 让 Vue 根据 weather 的值进行判断,选择将被渲染的标签

### 六,v-show 标签是否展示

```
<script setup></script>

<template>
  <p v-show="False">哈哈哈</p>
</template>

<style scoped></style>
```

- 和`v-if`不同,`v-show`所属的标签总是被加载的,若是指定其值为 False,但在网页源码中,他的属性只是`style="display: none;"`即不展示,而非不存在
- 也就是说`v-show`占用更多的初始渲染资源,但切换资源消耗更小,适合用在频繁切换的标签上
- 但`v-if`与之相反,加载更快,但切换更消耗资源

### 七,v-for 遍历数组和对象

```vue
<script setup>
import { reactive } from "vue";

// 定义数组
let books = reactive([
  { title: "三国演义", author: "罗贯中" },
  { title: "红楼梦", author: "草学期" },
  { title: "西游记", author: "吴承恩" },
  { title: "水浒传", author: "施耐庵" },
]);

// 定义对象
let person = reactive({
  name: "刘德华",
  age: 19,
});

const addBook = () => {
  books.push({ title: "金瓶梅", author: "老流氓" });
};

const popBook = () => {
  books.pop();
};

const shiftBook = () => {
  books.shift();
};

const unshiftBook = () => {
  books.unshift({ title: "金瓶梅", author: "老流氓" });
};
</script>

<template>
  <!-- 遍历数组 -->
  <table>
    <thead>
      <tr>
        <th></th>
        <th>书名</th>
        <th>作者</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(book, index) in books" :key="index">
        <td>{{ index + 1 }}</td>
        <td>{{ book.title }}</td>
        <td>{{ book.author }}</td>
      </tr>
    </tbody>
  </table>
  <div>
    <button @click="addBook">末尾添加图书</button>
    <button @click="popBook">删除末尾图书</button>
    <button @click="shiftBook">删除开头图书</button>
    <button @click="unshiftBook">开头添加图书</button>
  </div>

  <div>
    <!-- 遍历对象 -->
    <p v-for="(key, value, index) in person" :key="index">
      {{ index + 1 }}键:{{ key }},值:{{ value }}
    </p>
  </div>
</template>

<style scoped></style>
```

- 遍历数组元素:`v-for="(单数, 索引) in 总体"`
- 遍历对象属性:`v-for=(值, 键, 索引) in 对象`
- `:key` 是 `v-bind:key` 的缩写,是为了确保被循环标签的唯一性,以及标签内所含多个元素的统一性,如无特殊情况,一般必须指定该属性,同时建议指定为索引
- 通过`数组.push()`,`数组.unshift()`在末尾/开头添加元素,同时引发视图更新
- 通过`数组.pop()`,`数组.shift()`在末尾/开头删除元素,同时视图更新
- `Array.splice(起始下标, 结束下标, 更新内容)`
  - `books.splice(0, 2, {title: '书1', author: '作者1'}, {title: '书2', author:'作者2'})` => 意为,从第 0 个元素开始找,找 2 个元素,依次给他们赋新的值
  - `books.splice(0, 2)` => 意为,从第 0 个元素开始找,找到 2 个元素,给他们赋空值(即删除)
- `Array.reverse()`倒序排序

### 八,数组覆盖

```vue
<script setup>
// 数组覆盖
import { ref, reactive } from "vue";
let books = ref(["红楼梦", "西游记", "水浒传", "三国演义"]);

// ref定义的boos可以直接修改
const onUpdateBook = () => {
  books.value = ["金瓶梅", "小黄书"];
};

// reactive定义时,必须定义为对象,才能够进修修改
let jobs = reactive({
  // 还必须要个属性来存放数组
  value: ["送外卖", "跑滴滴", "当保安"],
});

// 这样才能进行修改
const onUpdateJob = () => {
  jobs.value = ["打老人", "爆金币"];
};

// 所以还是建议使用ref()定义数组
</script>

<template>
  <div>
    <p v-for="(book, index) in books" :key="index">{{ book }}</p>
    <button @click="onUpdateBook">点击修改图书</button>
  </div>
  <div>
    <p v-for="(job, index) in jobs.value" :key="index">{{ job }}</p>
    <button @click="onUpdateJob">点击修改工作</button>
  </div>
</template>

<style scoped></style>
```

- 建议使用`ref()`定义数组
- 如果使用`reactive()`定义,就必须指定其某一属性用于存放数组,一般还是叫`value`

### 九,v-on 绑定事件

```vue
<script setup>
import { ref } from "vue";
let count = ref(0);

const add = (step) => {
  count.value += step;
};

const goTo360 = (event) => {
  console.log(event);
  // event.preventDefault() //在下面第二个a标签通过 @click.prevent 进行默认事件的阻止
  window.location = "http://www.360.com";
};
</script>

<template>
  <div>
    <p>{{ count }}</p>
    <!-- v-on:click 就是 @click -->
    <!-- 如果逻辑不是特别复杂,不用=一个函数,直接写表达式 -->
    <button v-on:click="count++">+</button>
    <button @click="add(8)">+8</button>
  </div>
  <div>
    <!-- 通过阻止默认行为 -->
    <a href="www.baidu.com" @click="goTo360"
      >href指向百度,但我就要这个标签去360</a
    >
    <!-- 通过事件.prevent修饰符 -->
    <a href="www.baidu.com" @click.prevent="goTo360">就去360</a>
  </div>
</template>

<style scoped></style>
```

- `@click` 就是 `v-on:click`的语法糖缩写
- 除了`prevent`还有以下的事件修饰符:

  - `stop` => 阻止事件冒泡
  - `capture` => 捕获
  - `once` => 只执行一次
  - `self` => 指向被执行元素本身

### 十,模板上给标签指定 ref

- 普通 javascript 获取某个元素,需要`document.getElementById('指定id')`来获取元素,同时还需要 html 标签上指定 id
- Vue 给了我们更好的方法获取页面某个元素,通过`ref()`函数

```vue
<script setup>
import { ref } from "vue";
// 获取同名元素
let usernameInput = ref();

const getUsernameInput = () => {
  // usernameInput.value是input标签
  console.log(usernameInput.value.value);
  // 再.value才是input标签里面的值
};
</script>

<template>
  <label for="username">用户名</label>
  <!-- 这里ref必须和变量名一样 -->
  <input
    type="text"
    ref="usernameInput"
    name="username"
    placeholder="请输入用户名"
  />
  <button @click="getUsernameInput">点击获取用户名</button>
</template>

<style scoped></style>
```

- 这样的好处是,多人开发时,不会出现重复 id,因为 ref 被渲染后永远唯一

### 十一,v-model 绑定响应式变量

```vue
<script setup>
import { ref } from "vue";
let myText = ref("");
</script>

<template>
  <input type="text" v-model="myText" />
  <p>{{ myText }}</p>
</template>

<style scoped></style>
```

- 通过`v-model="指定响应式变量"`
- 会发现随着 input 标签的值的改变,p 标签也会随之改变

# 计算属性

```vue
<script setup>
// 导入 ref 和 computed
import { ref, computed } from "vue";

let width = ref(0);
let height = ref(0);

// 定义面积,为"计算类型"(),返回值为一个匿名函数
let area = computed(() => {
  // 匿名函数返回宽度*高度
  return width.value * height.value;
});
</script>
```

- 其余略

# 监听

- 抛砖引玉的使用场景:监听当前页实现分页功能,当页数变化时,重新向服务器发起请求,获取新数据并重新渲染页面

### 监听 ref 定义的变量

```vue
<script setup>
import { ref, watch } from "vue";

let count = ref(0);

// 使用watch()监听基本数据类型:count
watch(count, (oldValue, newValue) => {
  console.log(oldValue);
  console.log(newValue);
});

// 如何监听对象?
let person = ref({
  name: "刘德华",
  age: 19,
});

// 监听整个对象的改变
watch(person, (oldValue, newValue) => {
  console.log(oldValue);
  console.log(newValue);
});

// 使用getter()函数监听某一属性的变化
watch(
  () => person.value.name, //这个匿名函数就代表getter()
  (newValue, oldValue) => {
    console.log(oldValue);
    console.log(newValue);
  }
);

// 声明deep:true深度监听对象所有的变化
watch(
  person,
  (newValue, oldValue) => {
    console.log(oldValue);
    console.log(newValue);
  },
  { deep: true }
);

const onUpdateCount = () => {
  count.value++;
};

const onUpdatePerson = () => {
  person.value = {
    name: "张学友",
    age: 18,
  };
};
</script>
```

- 监听基本数据类型 `watch(基本数据, (新值, 旧值) => {函数体})`
- 监听对象整体改变 `watch(对象, (新, 旧值) => {函数体})`,**注意:**这样监听是当整个对象一次性发生变化时,也就是直接`person = 新值`时,才会被监听到
  > 如果单纯地修改`person.name`或者`person.age`,是不会被监听的
- 监听对象下的某个属性`watch(() => 对象.value.属性, (新, 旧值) => {函数体})`,第一个参数相当于`getter()`,监听对象的某一个属性
- 深度监听对象改变`watch(对象, (新, 旧值) => {函数体}, {deep: true})`,watch 函数增加第三参数`{deep:true}`,声明深度监听
  > 深度监听时,对象任何属性发生变化,都会被监听到

### 监听 reactive 定义的变量

```vue
<script setup>
import { reactive, watch } from "vue";

let university = reactive({
  name: "家里蹲",
  year: 1111,
});

// 监听由reactive定义的变量,默认就是深度监听
watch(university, (newValue, oldValue) => {
  console.log(newValue);
  console.log(oldValue);
});

// 如果想监听单个属性:同样的方式简写getter()
watch(
  () => university.name,
  (newValue, oldValue) => {
    console.log(newValue);
    console.log(oldValue);
  }
);
</script>
```

# 生命周期函数

```vue
<script setup>
import {
  onBeforeMount,
  onBeforeUnmount,
  onBeforeUpdate,
  onMounted,
  onUnmounted,
  onUpdated,
  ref,
} from "vue";

let count = ref(0);
const updateCount = () => {
  count.value++;
};

console.log("当前生命周期:setup");

// 挂载前
onBeforeMount(() => {
  console.log("当前生命周期:onBeforeMount");
});
// 挂载完成
onMounted(() => {
  console.log("当前生命周期:onMounted");
});

// 更新前
onBeforeUpdate(() => {
  console.log("当前生命周期:onBeforeUpdate");
});
// 更新完成
onUpdated(() => {
  console.log("当前生命周期:onUpdated");
});

// 卸载前
onBeforeUnmount(() => {
  console.log("当前生命周期:onBeforeUnmount");
});
// 卸载完成
onUnmounted(() => {
  console.log("当前生命周期:onUnmounted");
});
</script>
```

- 创建阶段:setup
- 挂载阶段:onBeforeMount, onMounted
- 更新阶段:onBeforeUpdate, onUpdated
- 卸载阶段:onBeforeUnmount, onUnmounted
- 主要作用就是在组件的某个阶段进行自动操作,比如在某个页面被打开的瞬间(onBeforeMount)时,请求服务器数据,然后渲染页面

# 自定义组件

### 定义属性

- `defineProps()`
- 父组件挂载子组件同时,给子组件如何传递参数,子组件如何接受呢?
- 父组件`MyComponent.vue`挂载子组件并传递参数

```vue
<script setup>
import { ref } from "vue";
import PersonComponent from "./PersonComponent.vue";

let name = ref("张三");
</script>

<template>
  <!-- 父组件给子组件传入参数的方法,就是直接:变量名="值" -->
  <PersonComponent :username="name" :body="{ height: 180, weight: 90 }" />
  <!-- 注意:传参前面的冒号: -->
</template>

<style scoped></style>
```

- 子组件`PersonComponent.vue` 接收参数并渲染模板

```vue
<script setup>
// const props = defineProps(['username'])

const p1 = defineProps({
  username: String,
  gender: {
    type: String,
    default: "男",
  },
  body: {
    height: Number,
    weight: Number,
  },
});

// 注意,defineProps()在一个vue文件中只能用一次.
// 注意,defineProps()接受父组件传入的参数后,不能够在子组件进行修改
</script>

<template>
  <!-- <p>姓名{{ props.username }}</p> -->
  <p>姓名:{{ p1.username }}</p>
  <p>性别:{{ p1.gender }}</p>
  <p>身高:{{ p1.body.height }}</p>
  <p>体重:{{ p1.body.weight }}</p>
</template>

<style scoped></style>
```

- 可以用数组进行接受
- 更推荐用对象进行接受,并且可以配置数据类型,指定默认值
- 注意:子组件不能随意修改父组件传入参数的值,所以建议用`const` 接受

### 定义事件

- 子组件定义事件,当自己内部发生变化时,告诉父组件
- 子组件`GoStep.vue`

```vue
<script setup>
import { ref } from "vue";

let step = ref(0);
const emit = defineEmits(["walk"]); //emit此时作为 defineEmits()的代理,walk作为事件名称, defineEmits(['可以定义多个事件'])

// 然后当我们点击走路按钮时,点击事件执行以下内容
const updateStep = () => {
  step.value++; // 走一步
  emit("walk", step.value); //告诉emit执行walk事件,在哪执行呢?父组件ShowStep.vue中
};
</script>

<template>
  <div>
    <p>子组件中的step:{{ step }}</p>
    <button @click="updateStep">走路</button>
  </div>
</template>

<style scoped></style>
```

- 父组件`ShowSteps.vue`

```vue
<script setup>
import GoStep from "./GoStep.vue";

const updateMystep = (step) => {
  console.log(`父组件接受的step是:${step}`);
};
</script>

<template>
  <!-- 子组件执行walk事件,那么父组件就调用updateMystep函数 -->
  <GoStep @walk="updateMystep" />
</template>

<style scoped></style>
```

- 通过这样的方式,子父组件实现了相互通信
  > 在最新版本的 vue 中,`defineProps`, `defineEmits`都不需要被导入,可以直接使用了

### v-model 双向绑定

- 在子组件`VmodelSon.vue`中

```vue
<script setup>
import { watch } from "vue";

let step = defineModel(); //定义双向绑定变量

watch(step, (newValue) => {
  console.log(`子组件监听到的step是${newValue}`);
});

const updateStep = () => {
  step.value++;
};
</script>

<template>
  <p>当前step:{{ step }}</p>
  <button @click="updateStep">在子组件中走1步</button>
</template>

<style scoped></style>
```

- 通过`defineModel()`定义一个响应式变量后
- 在父组件`VmodelFather.vue`中

```vue
<script setup>
import VmodelSon from "./VmodelSon.vue";
import { ref, watch } from "vue";

let step = ref(0); //定义同名响应式变量

watch(step, (newValue) => {
  console.log(`父组件监听到的step是${newValue}`);
});

const updateFatherStep = () => {
  step.value++;
};
</script>

<template>
  <!-- 并且在模板上声明该变量 -->
  <VmodelSon v-model="step" />
  <button @click="updateFatherStep">在父组件里走1步</button>
</template>

<style scoped></style>
```

- 如此一来,无论子组件还是父组件的 step 发生变化,他们内部的 step 都会同时改变.

# 插槽

### 默认插槽

- 子组件`SubmitButton.vue`声明槽孔

```vue
<!-- 这是一个带插槽的子组件 -->
<template>
  <button type="submit">
    <!-- 通过slot声明槽孔 -->
    <slot></slot>
  </button>
</template>
```

- 父组件`ShowButton.vue`填充插槽

```vue
<!-- 这是一个填充插槽的父组件 -->
<script setup>
import SubmitButton from "./SubmitButton.vue";
</script>

<template>
  <!-- "登录"填充slot -->
  <SubmitButton>登录</SubmitButton>
</template>
```

- 为什么这么用?提高代码的复用性

### 具名插槽

- 子组件`BaseLayout.vue`指定插槽

```vue
<!-- 基本布局 -->
<script setup></script>

<template>
  <div class="base_layout">
    <header>
      <!-- 不指定名称，就是默认插槽 -->
      <slot></slot>
    </header>
    <div>
      <!-- 指定名称，就是具名插槽 -->
      <slot name="title"></slot>
      <slot name="content"></slot>
    </div>
  </div>
</template>

<style scoped>
.base_layout {
  background-color: aqua;
}
</style>
```

- 父组件`FillLayout.vue`填充插槽

```vue
<!-- 填充插槽 -->
<script setup>
import BaseLayout from "./BaseLayout.vue";
</script>

<template>
  <BaseLayout>
    <!-- 填充默认插槽 -->
    <!-- 不用使用template标签 -->
    <!-- <p>我是默认插槽的填充内容</p> -->
    <!-- 如果非要使用 + #default -->
    <template #default="props">
      <!-- 如果默认插槽需要接收参数，就需要写template #default标签 -->
      {{ props.username }}
    </template>

    <!-- 填充具名插槽 -->
    <!-- 必须使用template标签 + #slot.name -->
    <template #title>
      <p>我是slot_title的填充内容</p>
    </template>
    <template #content>
      <p>我是slot_content的填充内容</p>
    </template>

    <!-- 具名插槽接受参数 -->
    <template #footer="props">
      {{ props.username }}
    </template>
  </BaseLayout>
</template>

<style scoped>
.base_layout {
  background-color: aqua;
}
</style>
```

- 默认插槽不包在`template`里，除非需要接受参数，就用`<template #default="props>"`包内容
- 具名插槽**必须**包在`<template #slot.name>,,,填充内容,,,</template>`里
- 具名插槽接受参数`<template #slot.name="props">现在访问具体参数{{ props.参数名 }}</template>`
