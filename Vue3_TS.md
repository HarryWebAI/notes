# 创建工程

### 基于 vite 创建

- 了解`vite`: 就是一个比`webpack`**更快**的构建工具
- 基于`vite`创建: `npm create vue@latest`
- 输入项目名称, 勾选需要的库...
- 进入项目`npm i`: 安装依赖
- `npm run dev`: 跑起来

### vsCode 小技巧

- 输入`vue`直接写好 vue 模板: `文件 → 首选项 → 用户代码片段`, 输入`vue`, 打开`vue.json`, 编辑

```json
{
  "Print to console": {
    "prefix": "vue",
    "body": [
      "<template>",
      "</template>",
      "",
      "<script setup lang=\"ts\">",
      "</script>",
      "",
      "<style scoped>",
      "</style>"
    ],
    "description": "Vue单文件组件模板"
  }
}
```

- 自动补充`.value`: `Ctrl+,`打开设置, 找到`扩展`, `Vue`, 找到`Auto Insert Dot Value`(自动插入.value)

### vue3 和 2 的区别?

1. `vue2`是`选项式api`, 写`name, data, methods, computed`
2. `vue3`式`组合式api`, 数据,方法,计算属性全部写在一个`setup`里面

- 相对来说, `vue2`想要新增或者增加一个需求, 就需要分别修改几个选项, 不便于维护和复用
- 其实, `vue3`的 setup 语法糖, 在`vue2`中是这样的:

```vue
<script>
export default {
  setup() {
    // ...
  },
};
</script>
```

- 注意! `setup`执行的顺序在`data, methods`这些选项之前, 假如在 setup 里定义变量`a`, 那么 data 里可以通过`this.a`获取该变量
- 但是 `setup` 里的`this` 是`undefined`
- 也就是说: 2,3 两种语法可以共存, 且 2 的写法可以读取 3setup 里面的东西, 但是 3 不能读 2 的(了解即可)
- 想要自定义组件名?

```vue
<script lang="ts">
export default {
  name: "新的组件名",
};
</script>

<script setup lang="ts">
// 业务逻辑...
// 数据..
// 方法..
</script>
```

- 或者 `npm i vite-plugin-vue-setup-extend -D`安装一个插件后, 编辑`vite.config.ts`

```ts
import { fileURLToPath, URL } from "node:url";

import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import vueDevTools from "vite-plugin-vue-devtools";
import VueSetUp from "vite-plugin-vue-setup-extend";

// https://vite.dev/config/
export default defineConfig({
  plugins: [vue(), vueDevTools(), VueSetUp()],
  resolve: {
    alias: {
      "@": fileURLToPath(new URL("./src", import.meta.url)),
    },
  },
});
```

- **现在 Vue 更推荐多个单词组成组件名**, 比如`XxxComponent.vue`, `XxxView.vue`, 这样就不用重命名了

# 响应式数据

> 在 Vue2 中, data 这个选项里的所有变量就是响应式的

### ref

1. 导入: `import {ref} from 'vue'`
2. 想让哪个是响应式的,就用 `ref()` 包哪个 => 该变量的数据类型会变成 `RefImpl实例对象`
3. `<script>`里面读取: `xxx.value`
4. `<template>`里面读取: `{{ xxx }}`

### reactive

1. 导入: `import {reactive} from 'vue'`
2. 定义对象类型的响应式数据 `reactive({ })` => 该变量的数据类型会变成 `Proxy代理对象`
3. `<script>` 和 `template` 里面都不用 `.value` 就能读取
4. reactive 定义的对象是深层次的, a 包 b 包 c 包对象包数组, 再里面也都能被读取和修改.

> js 里面: 数组, 函数, 这些都是对象

### ref 和 reactive 的区别

- `ref` 可以定义基本类型数据, 也可以用来定义对象类型数据

> 但是 ref 定义对象类型的响应式数据时, 底层也还是用的 reactive 的逻辑 (可以这样理解: ref.value ==> reactive)

- `reactive` 只能定义对象类型数据

  1. 若需要一个基本类型的响应式数据, 必须用`ref`
  2. 若需要一个响应式对象, 建议使用`reactive`

- `reactive` 的局限性:

```vue
<script setup lang="ts">
import { reactive } from "vue";

let obj1 = reactive({ name: "刘德华" });

// obj1 = {name:'张学友'} // 错误! 重新赋值整个对象, 会直接导致整个对象失去"响应式"
obj1.name = "张学友"; // 可以修改单属性
Object.assign(obj1, { name: "张学友" }); // 使用 Object.assign() 更好: 分配第二参数对象的同名字段到第一对象的同名字段中
</script>
```

- `ref` 没有上面的问题, 可以整个替换

### toRef / toRefs

> 实现取出响应式对象里的所有属性时, 使取出属性继续维持响应式

```vue
<script setup lang="ts">
import { reactive, toRefs, toRef } from "vue";

const person = reactive({
  name: '刘德华',
  age: 18
})

// 解构赋值, 取得name和age, 但此时 name 和 age 并不是响应式的
// const {name, age} = person

// 这样就行了, 此时 name = ref('刘德华'), age = ref(18), 读取得 .value
cosnt {name, age} = toRefs(person)

// 关于 toRef, 单个取出, 有点麻烦
const name1 = toRef(person, 'name')
</script>
```

# 计算属性

```vue
<template>
  <h2>姓</h2>
  <input type="text" v-model="firstName" />
  <h2>名</h2>
  <input type="text" v-model="lastName" />
  <h2>全名:</h2>
  <input type="text" v-model="fullName" />
</template>

<script setup lang="ts">
// 引入 computed
import { ref, computed } from "vue";

const firstName = ref("张");
const lastName = ref("三");

// 定义计算属性 computed(() => { return 出去最终的计算结果 })
const fullName = computed(() => {
  return firstName.value + lastName.value;
});
</script>
```

1. 计算属性是只读的, 且比函数更灵活, 它具有"缓存"机制: 只有当参与计算的变量发生改变时, 它才会被调用
2. 计算属性的数据类型是`ComputedRefImpl对象`, 说白了还是一个`Ref`
3. 计算属性想要可以读写, 需要写`get(){}` 和 `set(){}`

```js
const fullName = computed(() => {
  get() {
    return firstName.value + lastName.value
  }

  set(newValue) {
    return fullName.value = newValue
  }
})
```

# watch 监视数据变化

- 导入使用: `import {watch} from 'vue'`

### 情况 1: 监视 ref 定义的普通类型数据

```js
watch(要监视的数据不用.value, (新值, 旧值) => {
  // 当被监视数据发声变化时, 做些什么.
});
```

### 情况 2: 监视 ref 定义的引用类型(对象类型)数据

```js
const person = ref({
  name: "张三",
  age: 18,
});

// 如何这样监视person, 只有当person整体改变时, 才会触发回调
// watch(person, (newVal, oldVal) => {
//   console.log(newVal, oldVal)
// })

// 解决方案1: 深度监听, 配置第三参数 {deep: true}
watch(
  person,
  (newVal, oldVal) => {
    console.log(newVal, oldVal);
  },
  { deep: true, immediate: true }
); // immediate: true 立即执行一次

// 解决方案2: getter()函数监视指定的单个基本类型数据的属性 () =>  person.name
watch(
  () => person.value.name,
  (newVal, oldVal) => {
    console.log(newVal, oldVal);
  }
);
```

- 为了监视 ref 定义的响应式对象内部的变化, 要么开启深度监听: `{deep: true}`
- 要么函数指定监听某个属性: `() => 具体属性`
- `immediate: true` => 会立刻执行一次监听

### 情况 3: 监视 reactive 定义的响应式数据

- `reactive` 只能定义引用数据(对象, 数组...)类型的响应式数据
- 且`reactive` 定义的数据, 被监听时, 默认开启深度监听, 且不能关闭 (不配置 deep:true, 也是深度监听)

### 情况 4: 监听对象属性里的嵌套对象里的单个属性时的最佳解决方案

```js
const userInfo = reactive({
  name: "张三",
  job: {
    name: "前端工程师",
    salary: 10000,
  },
});

// 这样监听不会报错, 但当userInfo.job整体改变时, 会失去监听(因为其内存地址改变了)
watch(userInfo.job, (newVal, oldVal) => {
  console.log(newVal, oldVal);
});

// 所以最优解: 监视对象里的单个属性, 就用函数式, 且如果该属性是个嵌套的对象, 需要关注其内部属性, 则需要配置{deep: true}
watch(
  () => userInfo.job,
  (newVal, oldVal) => {
    console.log(newVal, oldVal);
  },
  { deep: true }
);
```

- 原则 1, 监听对象里的单个属性就用`() => obj.指定到具体属性上`
- 原则 2, 监听的该属性还是个对象, 就配置`{deep: true}`

### 情况 5: 监听多个属性

```js
watch([() => userInfo.name, () => userInfo.job], (newVal, oldVal) => {
  console.log(newVal, oldVal); // 新值旧值都是数组
});
```

- `[()=>指定属性1, ()=>指定属性2]`, 此时回调函数里的新值, 旧值都是这样格式的数组

# watchEffect

- demo

```vue
<template>
  <h2>需求: 当水温达到60℃, 或者水位达到80cm时, 发出警报</h2>
  <p>当前水温: {{ temp }}℃</p>
  <p>当前水位: {{ level }}cm</p>
  <button @click="temp += 10">点我升温</button>
  <button @click="level += 10">点我加水</button>
</template>

<script setup lang="ts">
import { ref, watch, watchEffect } from "vue";

const temp = ref(0);
const level = ref(0);

// 用 watch 实现发出警报
// watch([temp, level], (values) => {
//   const [newTemp, newLevel] = values
//   if (newTemp >= 60) {
//     console.log('watch监听发出水温警报')
//   }

//   if (newLevel >= 80) {
//     console.log('watch监听发出水位警报')
//   }
// })

// 用 watchEffect 实现发出警报
watchEffect(() => {
  if (temp.value >= 60) {
    console.log("watchEffect监听发出水温警报");
  }

  if (level.value >= 80) {
    console.log("watchEffect监听发出水位警报");
  }
});
</script>

<style scoped></style>
```

- 一句话: `watchEffect` 不需要指定监听的对象, 直接传回调函数进去, 它会自动识别函数内部用到的数据, 自己去监听这些数据

# 标签上的 ref

- 加在普通的 html 标签上, 相当于打上只在本组件生效的 id
- 加在组件标签上, 拿到的是"受保护"的组件实例, 如果想要组件暴露内部的信息, 还得使用`defineExpose({想要暴露出去的数据})` (了解即可)
- 比较蛋疼的规则, `ref` 可以被看做是一个特殊的标识符, 它加在标签上不用`:ref="..."`
- 但我们应该知道, 通常来说, 一个标签上 `<p a="1+1" :b="1+1" / >`, **加冒号的是表达式, 它会进行执行**, 不加冒号的就是普同字符串

# 在 Vue 中使用 typescript

- 约定俗成, 新建 `@/types/index.ts`, 在里面规定数据的格式

```ts
// 暴露并定义一个接口, 叫做 PersonInterface
export interface PersonInterface {
  // 必须符合以下格式
  id: string;
  name: string;
  age: number;
}

// 暴露并定义一个类型, 叫做 Persons类型是泛型: 由<PersonInterface>组成的数组
// export type Persons = Array<PersonInterface>
// 也可以这样写
export type Persons = PersonInterface[];
```

- 在实际投入使用时

```ts
<script setup lang="ts">
// 引入类型限制
// import { type PersonInterface, type Persons } from '@/types'
import type { PersonInterface, Persons } from '@/types'

// 定义单个对象, 符合PersonInterface定义的规范
const person: PersonInterface = {
  id: '1',
  name: '张三',
  age: 18
}

// 定义多个对象组成的列表, 符合Persons定义的规范(由PersonInterface格式组成的数组)
const persons: Persons = [
  {
    id: '1',
    name: '张三',
    age: 18
  },
  {
    id: '2',
    name: '李四',
    age: 20
  },
  {
    id: '3',
    name: '王五',
    age: 22
  }
]
</script>
```

- 通常一个大型项目, 都是将数据格式规范定义在 `@/types/` 中, 叫 `index.ts` 是为了方便, 导入时可以不指定为 `@/types/index.ts`
- 导入需要`import type {接口, 自定义类型}`, 或者`import {type 接口, type 自定义类型}`

# props

> 父组件如何往子组件传递数据?

- 父组件传递参数: `<子组件 :key="value" />`
- 子组件接收参数:

```vue
<template>
  <ul>
    <li v-for="person in personList" :key="person.id">
      {{ person.id }} -- {{ person.name }} -- {{ person.age }}
      <span v-if="person.car">-- {{ person.car }}</span>
    </li>
  </ul>
</template>

<script setup lang="ts">
import { type Persons } from "@/types";
// import { defineProps, withDefaults } from 'vue';

// 接收父组件传递过来的数据
// defineProps(['personList'])

// 接收父组件传递过来的数据, 并加上类型限制
// const props = defineProps<{
//   personList: Persons
// }>()

// console.log(props)

// 接收父组件传递过来的数据, 父组件可传可不传, 不传拥有默认值
withDefaults(
  defineProps<{
    personList: Persons;
  }>(),
  {
    personList: () => [
      {
        id: "1",
        name: "张三",
        age: 18,
      },
    ],
  }
);
</script>
```

1. 如果使用 ts, 那么子组件接收父组件传递进来的数据, 格式就是`defineProps<{key:type, key:type}>()`
2. 注意, `defineProps, withDefaults, defineExpose` 这些**宏函数**其实不用特意引入

# 生命周期

> 又叫生命周期函数, 生命周期钩子

### 组件的生命周期

1. 创建
2. 挂载
3. 更新
4. 销毁

### vue2 的 生命周期

- vue2 里的生命周期

```vue
<script>
export default {
  name: 'lifeCycle',
  // 数据
  data(){},
  // 方法
  method(){},

  /** 生命钩子 */
  beforeCreate() {
    console.log('组件创建前')
  },
  created() {
    console.log('组件创建完毕')
  }
  beforeMount() {
    console.log('组件挂载前')
  }
  mounted() {
    console.log('组件挂载完毕')
  }
  beforeUpdate() {
    console.log('组件更新前')
  }
  updated() {
    console.log('组件更新完毕')
  }
  beforeDestroy() {
    console.log('组件销毁前')
  }
  destroyed() {
    console.log('组件销毁完毕')
  }
}
</script>
```

- 当页面发生变化时, 就会触发`更新`周期
- 想要触发`销毁`周期, 可以通过`v-if"`实现, 但`v-show`不行, 因为`v-show`只是给组件添加了样式`display: none`, 而`v-if`直接干掉了整个组件
- **和 vue3 的区别**: 着重注意钩子函数的名称, 钩子函数的写法`生命周期(){ }`

### vue3 的生命周期

```vue
<template>
  <h1>生命周期函数</h1>
</template>

<script setup lang="ts">
// 创建前, 没有了, 因为setup就直接执行了创建前和创建完毕
console.log("创建前");
console.log("创建完毕");

import {
  onBeforeMount,
  onMounted,
  onBeforeUpdate,
  onUpdated,
  onBeforeUnmount,
  onUnmounted,
} from "vue";

onBeforeMount(() => {
  console.log("挂载前");
});

onMounted(() => {
  console.log("挂载后");
});

onBeforeUpdate(() => {
  console.log("更新前");
});

onUpdated(() => {
  console.log("更新后");
});

// 没有'销毁'了, 叫'卸载' => unmount
onBeforeUnmount(() => {
  console.log("卸载前");
});

onUnmounted(() => {
  console.log("卸载后");
});
</script>

<style scoped></style>
```

- `setup` 就是 vue2 中的`创建前..., 创建完毕...`
- 父组件和子组件的生命周期谁先谁后? **App 根组件永远是最晚挂载完的**, 子组件的生命周期都先于父组件.
- 常用的钩子: `挂载前`(比如从服务器获取原始数据), `更新前`(比如保留更新前的数据), `卸载完毕`(比如备份组件内部的数据)

# 自定义 hooks

> 将统一模块所需要使用的数据, 函数封装在一起

- 约定俗成, 将所有自定义 hooks 放在`@/hooks/`中
- 约定俗成, 所有的 hooks 应该取名为`useXxx.ts`, Xxx 就是模块名称, 比如 `useSum`, `useOrder`, ...
- 一个标准的 hooks 文件:

```ts
import { ref, onMounted } from "vue";

export default function () {
  // 数据
  const sum = ref(0);

  // 方法
  const add = () => {
    sum.value++;
  };

  // 生命周期
  onMounted(() => {
    console.log(`挂载后sum初始值为${sum.value}`);
  });

  // 交出去
  return { sum, add };
}
```

- 在组件中投入使用:

```vue
<template>
  <!-- 数据可以直接渲染 -->
  {{ sum }}
  <!-- 方法也可以直接调用 -->
  <button @click="add">加1</button>
</template>

<script setup lang="ts">
// 引入hooks
import useSum from "@/hooks/useSum";

// 解构赋值获取hooks内部的数据和方法
const { sum, add } = useSum();
</script>

<style scoped></style>
```

# 路由

### 什么是路由

1. Vue, React 这些前端框架, 都是为了构建 SPA 而开发出来的, 所谓`SPA`就是`Single Page Application`, 单页应用, 开发完了就一个`index.html`
2. 为了单一页面渲染不同数据, 就需要使用`路由`, 其最直观的作用就是通过 url 地址的变化, 在指定区域(路由出口)渲染不同的组件
3. 比如我之前开发的 myErp, 路由出口就在 `MainView.vue`中的`main`部分, 所以当我在侧边栏点击不同的功能时, `1浏览器地址变化`, `2渲染指定的路由`

### 路由投入使用

1. 请来路由器: `npm i vue-router`
2. 新建路由器, 配置路由规则, 新建`@/router/index.ts`

```ts
// 引入路由器创建函数和工作模式定义函数
import { createRouter, createWebHistory } from "vue-router";
// 引入路由组件
import Home from "@/components/HomeView.vue";
import News from "@/components/NewsView.vue";
import About from "@/components/AboutView.vue";

// 创建路由器
export const router = createRouter({
  // 配置路由的工作模式
  history: createWebHistory(),
  // 配置路由
  routes: [
    {
      path: "/home",
      component: Home,
    },
    {
      path: "/news",
      component: News,
    },
    {
      path: "/about",
      component: About,
    },
  ],
});
```

3. 声明路由器投入使用 `@/main.ts`

```ts
import { createApp } from "vue";
import App from "./App.vue";
// 引入路由器
import { router } from "./router";

// 创建应用实例
const app = createApp(App);

// 声明应用要使用路由器
app.use(router);

// 最后挂载应用
app.mount("#app");
```

4. 在组件上投入使用: 确定导航区, 确定展示区

```ts
<template>
  <div>
    <h2> 路由测试 </h2>
    <!-- 导航区 -->
    <div class="navigate">
      <RouterLink to="/home" active-class="active">首页</RouterLink>
      <RouterLink to="/news" active-class="active">新闻</RouterLink>
      <RouterLink to="/about" active-class="active">新闻</RouterLink>
    </div>
    <!-- 展示区 -->
    <div class="main-content">
      <RouterView />
    </div>
  </div>
</template>

<script setup lang="ts">
import { RouterView, RouterLink } from 'vue-router';
</script>

<style scoped>
.active {
  color: red;
}
</style>
```

- `RouterLink.active-class` => 路由激活的样式
- `<RouterView />` => 路由出口, 也可以写成`<router-view></router-view>`并且不用导入
- 路由切换时, 其实是对组件的挂载和卸载(可以通过生命钩子验证)

### 工程化项目目录结构上的约定俗成

1. 一般来说, 一般组件放在 `@/components/`中
2. 页面组件通常放在 `@/views/` 或者 `@/pages/` 中

> 所谓**页面组件**, 又叫做**路由组件**, 也就是声明在路由文件中的组件, 说白了就是具体的页面, 通常由各种 components 组成

### 路由的两种工作模式

1. history: 路径里没有`#`, 但项目投入生产环境, 需要配合服务器处理路径问题, 不然刷新可能出现 404
2. hash: 路径里有`#`, 项目投入使用也没问题, 但路径难看, 且**SEO 优化相对较差\*\***: `history: createWebHashHistory()`

- 解决方法(Nginx)

```
server {
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

> 一般来说都用第 1 种, 一些后台管理项目喜欢用第 2 种

### to 的两种写法

- 带冒号的: `:to = "{name/path: '名称或者路径'}"`
- 不带冒号的: `to="path"`

```vue
<RouterLink :to="{ path: 'home' }" active-class="active">首页</RouterLink>
<RouterLink to="/about" active-class="active">新闻</RouterLink>
```

- 一般来说,都用 `:to="{ name: '路由配置里路由的name' }"`

### 路由传参

1. query 传参(体现在浏览器地址栏里: `.../?key=value&key=value...`)

```vue
<!-- 传参: 方法1, 拼字符串 -->
<li v-for="item in newsList" :key="item.id">
  <RouterLink :to="`/news/detail/${item.id}?title=${item.title}&content=${item.content}`">
    {{ item.title }}
  </RouterLink>
</li>

<!-- 传参: 方法2, 配置query属性 -->
<li v-for="item in newsList" :key="item.id">
  <RouterLink :to="{
    name: 'newsDetail',
    
    query: { id: item.id, title: item.title, content: item.content }

  }">
    {{ item.title }}
  </RouterLink>
</li>

<!-- 接收: 引用routeHooks, 然后route.query.key获取value -->
<script setup lang="ts">
import { useRoute } from "vue-router";

const route = useRoute();

console.log(route.params.id);
console.log(route.query.title);
console.log(route.query.content);
</script>
```

2. params 传参 (体现在浏览器地址里: `.../value/value/value...`)

```js
// 1, 必须在路由配置文件里面占位, 并且必须配置路由名称
{
  name: 'newsDetail',
  path: 'detail/:id/:title/:content',
  component: NewsDetail,
},

// 2, 传参时必须使用路由名称, 而不能用path, 且传参数使用 params属性
<li v-for="item in newsList" :key="item.id">
  <RouterLink :to="{
    name: 'newsDetail',
    params: { id: item.id, title: item.title, content: item.content }
  }">
    {{ item.title }}
  </RouterLink>
</li>

// 3, 取值: 引用 routeHooks, 然后route.params.路由配置里面声明的占位
import { useRoute } from 'vue-router';

const route = useRoute();

console.log(route.params.id);
console.log(route.params.title);
console.log(route.params.content);
```

- 注意点 1: params 传参 to 里面必须用`name`
- 注意点 2: params 传参不能传引用数据类型的数据(对象, 数组这些都不行, 会报错)
- 注意点 3: params 传参如果参数可选, 需要在路由配置文件里面声明占位时加上问号: `/:id/:content?`

### 路由的 props 属性

> 上面的代码可以更优雅, 只需要以下步骤

1. 在路由声明文件`@/router/index.ts`中

```ts
{
  name: 'newsDetail',
  path: 'detail/:id/:title/:content',
  component: NewsDetail,
  props: true, // 开启 props
},
```

2. 然后在组件中, 通过`defineProps`接收

```vue
<template>
  <h1>{{ title }}</h1>
  <p>{{ content }}</p>
</template>

<script setup lang="ts">
// 通过 defineProps 接收参数, 可以直接在页面上 {{ title }} 进行渲染
defineProps<{
  title: string;
  content: string;
}>();
</script>
```

3. 以上的操作只适用于`params`传参, 如果想用`query`传参, 需要这样写配置文件

```ts
{
  name: 'newsDetail',
  path: 'detail/:id/:title/:content',
  component: NewsDetail,
  // 以函数的形式
  props(route) {
    return {
      id: route.params.id,
      title: route.params.title,
      content: route.params.content,
    }
  }
},
```

4. 还有一种特殊情况, 就是传递"死"值, 也就是把 props 写成一个对象 `props: {写死的key: 写死的value}`, 一般不用

### 路由的 replace 属性

1. 默认是`push`: 有历史记录, 可以用浏览器前进后退
2. 可以设置为`replace`: 没有历史记录, 浏览器不能后退: 在模板上声明即可: `<RouterLink replace :to="{ path: 'home' }" active-class="active">首页</RouterLink>`

### 编程式导航

> 所有的 `RouterLink` 都是组件, 底层都是`<a>`标签, 所谓编程式路由导航, 就是脱离 `RouterLink`

- 举例

```js
<!-- 需求: 按钮也可以实现路由跳转 -->
<RouterLink replace :to="{ path: 'home' }" active-class="active">首页</RouterLink>
<button @click="goHome">点我也能去首页</button>

<!-- 实现 -->
import { useRouter } from 'vue-router';

const router = useRouter();

const goHome = () => {
  router.push({ path: 'home' });
};
```

1. 引入路由器钩子 `useRouter`
2. 实例化路由器
3. 使用实例的方法`.push`, `.replace` 都可以, 里面传对象, 直接写路径都也都可以, 对象里面传不传参数, 怎么传, 也就是`RouterLink`的 to 怎么写, 这里就怎么写.

### 重定向

> 让指定的路径, 重新定位到另一个路径

- 记得我的项目 myErp 里面, 首页进来之后它不自动加载`/home`这个 path 指定的首页组件, 为了解决这个问题:

```js
path: '/',
name: 'main',
component: MainView,
// 根路由重定向到 home 组件即可解决
redirect: { name: 'home' },
```
