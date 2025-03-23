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
4. 如果 reactive 定义的对象是深层次的, a 包 b 包 c 包对象包数组, 再里面也都能被读取和修改.
5. 如果 reactive 里面再定义 ref 属性, 后要去读取里面的属性, 就不用再`.value`了.

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

### ref 什么时候该.value?

```ts
const obj = reactive({
  a: 1
  b: ref(2)
})
const x = ref(3)
```

1. 在`script`标签里, 单独的 ref, 需要`.value`, 比如 x 就需要`x.value`
2. 在`template`标签里, 单独的 ref, 不需要`.value`, 就比如 x 在模板里只需要`{{ x }}`就可以读取值
3. 在`script`标签里, reactive 里的 ref, 也不需要`.value`, 比如`obj.b`

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

### 导航守卫

1. 全局导航守卫: `router.beforeEach`, `router.afterEach`
2. 组件内导航守卫: `beforeRouteEnter`, `beforeRouteUpdate`, `beforeRouteLeave`
3. 路由独享守卫: `beforeEnter`
4. 每个导航守卫函数都有两个必要参数`((to, from) => {})`, to: 去哪, from: 从哪里走

# pinia

> pinia 实现集中式状态(数据)管理: `(react)redux`, `(vue2)vuex`, `**pinia(vue3)`

- 多个组件共用相同的数据(比如用户登录信息)时, 再使用 pinia

### 投入使用

1. 安装`npm i pinia`
2. 在`main.ts`中声明使用

```ts
// ...
// 引入pinia创建函数
import { createPinia } from "pinia";

// 创建应用实例
const app = createApp(App);

// 创建pinia实例
const pinia = createPinia();

// 使用pinia
app.use(pinia);
```

3. 新建`@/store/`文件夹
4. 新建`count.ts` => 命名规范: 小写

```ts
// 导入defineStore
import { defineStore } from "pinia";

// 定义并暴露store
export const useCountStore = defineStore("count", {
  // 存储数据的状态
  state() {
    return {
      count: 0,
    };
  },

  // 修改数据的方法
  actions: {
    increment() {
      this.count++;
    },
    decrement() {
      this.count--;
    },
  },
});
```

5. 投入使用

```vue
<template>
  计算组件
  <div>
    <button @click="countStore.increment">+</button>
    <span>{{ countStore.count }}</span>
    <button @click="countStore.decrement">-</button>
  </div>
</template>

<script setup lang="ts">
// 导入store
import { useCountStore } from "@/store/count";

// 实例化后即可投入使用, 读取state, 调用函数等, 直接使用 .state, .functionName 即可
const countStore = useCountStore();
// console.log(countStore) // 这个countStore是一个reactive定义的响应式对象
</script>
```

### 修改数据的三种功能方式

1. 单个修改:直接修改(当逻辑非常简单, 且只在该组件内使用时推荐)
2. 批量变更: .$patch({})

```ts
// 第一种方法
function add() {
  countStore.count++;
}

function sub() {
  countStore.count--;
}

// 第二种方法, 批量修改
function reset() {
  countStore.$patch({
    count: 0,
    // 如果还有其他state...
  });
}
```

3. store 文件内定义 actions(如上, 注意 this)

> 什么时候用 actions? 很简单, 当这个**函数频繁复用**时, 就用 actions 定义.

### storeToRefs

> 为了代码优雅, 我们会采用解构赋值取出 store 里的 state, 为了保证这些 state 具有响应式, 我们可能会使用 `toRefs()`, 但这回导致 store 里所有的东西, 都变成响应式对象!

- pinia 想到了这些, 所以提供`storeToRefs()`用以代替`toRefs()`,

```ts
import { useCountStore } from "@/store/count";
// 这里引入该函数
import { storeToRefs } from "pinia";

const countStore = useCountStore();
// console.log(countStore) // 这个countStore是一个reactive定义的响应式对象

// 这里解构赋值获取state.count, 同时注意storeToRefs()只会将该store里的state变成响应式, **只会关注数据**
const { count } = storeToRefs(countStore);
```

> 这里看情况吧, 我还是更喜欢 `store.StateName` 来直接取值...

### getters

> `数据加工`: 比如我的 myErp 项目, 需要生成库存商品的 full_name:(商品名称+尺寸+颜色), 就可以在 getters 里配置

- 示例代码

```ts
import { defineStore } from "pinia";

// 官方建议用hooks的形式来使用store
export const useCountStore = defineStore("count", {
  // ...定义了数据count

  // 定义数据加工
  getters: {
    // countStore 将多出一个属性 .doubleCount, 它返回count的两倍
    doubleCount: (state) => state.count * 2,
    // doubleCount: (state) => {
    //   return state.count * 2
    // },
  },
});
```

### .$subscribe

> 类似 `watch()`

- demo: 实现数据持久化

```ts
// 组件里
countStore.$subscribe((mutation, state) => {
  console.log(mutation, state)
  // 实现数据持久化(存入localStorage后, 关闭或刷新浏览器数据还在)
  localStorage.setItem('count', JSON.stringify(state.count))
})

// store里
state() {
  return {
    count: localStorage.getItem('count') ? JSON.parse(localStorage.getItem('count')!) : 0,
  }
},
```

- 在组件里, 当`countStore`里的数据发生变化时, 我们就自动执行`$subscribe()`内部的回调函数(类似于 watch): 将数据写入 localStorage
- 在 store 里, 我们尝试从 localStorage 里获取数据, 如果没有, 则初始化为 0
- localStorage 里面只能存字符串, 所以我们要将数据转为 json 字符串, 取得时候再解成原本得数据类型
  - `localStorage.setItem(key, value)` => 存入 localStorage
  - `localStorage.getItem(key)` => 通过键名获取 localStorage 里存储的数据
  - `JSON.stringify()` => 将括号内的数据转为 json 字符串
  - `JSON.parse()` => 将 json 字符串转为可用的数据

### 重点: Vue3 里 Pinia 的写法

> 上面其实都是选项式的(vue2)! 我们应该使用组合式的(vue3)!

- 举例

```ts
import { defineStore } from "pinia";
import { ref } from "vue";

export const useCountStore = defineStore("count", () => {
  // 定义属性
  const count = ref(0);

  // 定义方法
  function increment() {
    count.value++;
  }

  function decrement() {
    count.value--;
  }

  function reset() {
    count.value = 0;
  }

  // 记得交出去
  return {
    count,
    increment,
    decrement,
    reset,
  };
});
```

- 第二参数是一个函数, 不再是对象
- 就是要**记得交出去**

# 组件通信

### props: 父子通信

> 父传子很简单, 直接交给子组件. 子传父就需要父组件交给儿子一个函数, 儿子调用这个函数

1. 父组件交给儿子

```vue
<template>
  <div class="father">
    <h3>父组件</h3>
    <h4>汽车：{{ car }}</h4>
    <h4 v-show="toy">子给的玩具：{{ toy }}</h4>
    <!-- 就在这里传递即可 :key=属性 :key=方法都行 -->
    <Child :car="car" :sendToy="getToy" />
  </div>
</template>

<script setup lang="ts" name="Father">
import Child from "./Child.vue";
import { ref } from "vue";

// 数据
const car = ref("奔驰");
const toy = ref("");
// 方法
function getToy(value: string) {
  toy.value = value;
}
</script>
```

2. 子组件接收, 以及通过父组件提供的函数传递数据给父组件

```vue
<template>
  <div class="child">
    <h3>子组件</h3>
    <h4>玩具：{{ toy }}</h4>
    <h4>父给的车：{{ car }}</h4>
    <button @click="sendToy(toy)">把玩具给父亲</button>
  </div>
</template>

<script setup lang="ts" name="Child">
import { ref } from "vue";
// 数据
const toy = ref("奥特曼");

// 接收父组件传递的数据: defineProps
defineProps<{
  // 接收属性
  car: string;
  // 接收方法
  sendToy: (toy: string) => void;
}>();
</script>
```

### defineEmits: 子传父

- 先理解父组件里这句代码: `<Child @send-toy="saveToy" />`
  - 这是父组件在挂载子组件
  - 这是父组件定义了一个事件, 叫`send-toy`
  - 当 okok 被触发时, 会调用`saveToy`这个函数
- 子组件

```vue
<template>
  <div class="child">
    <h3>子组件</h3>
    <h4>玩具：{{ toy }}</h4>
    <button @click="emit('send-toy', toy)">测试</button>
  </div>
</template>

<script setup lang="ts" name="Child">
import { ref } from "vue";
// 数据
const toy = ref("奥特曼");
// 声明事件
const emit = defineEmits(["send-toy"]);
</script>
```

- 理清逻辑:

  - 父组件自定义事件 `send-toy` 交给子组件 (父亲先`@abc="xyz"`)
  - 子组件通过`const emit = defineEmits(["send-toy"])` 声明父组件的自定义事件 (儿子再`const emit = defineEmits(['abc'])`)
  - 子组件通过`<button @click="emit('send-toy', toy)">测试</button>` 调用了父组件的自定义事件 (儿子在合适的地方`emit('abc', 参数?)`)
  - 这个事件会导致父组件触发函数`saveToy`() (父亲的`xyz(参数?)`通过儿子传过来的东西实现逻辑)
  - 以此实现了子传父

- 子接收自定义事件: `const emit = defineEmits(["自定义事件名称", "可以接收多个自定义事件", "..."])`
- 子组件调用该事件并传参 `emit('事件名称', 参数)`

> Vue 官方推荐自定义事件使用 `abc-abc`的"肉串 kebab-case"命名方式

### mitt: 任意组件通信

1. 安装 `npm i mitt`
2. 约定俗成, mitt 相关代码应该放在 `@/utils/` 中使用
3. 示例: 创建 `emiiter.ts`

```ts
// 引入mitt
import mitt from "mitt";

// 调用mitt得到emitter，emitter能：绑定事件、触发事件
const emitter = mitt();

// 暴露emitter
export default emitter;
```

4. 使用

```vue
// 任意组件1: 提供数据
<template>
  <div class="child1">
    <h3>子组件1</h3>
    <h4>玩具：{{ toy }}</h4>
    <!-- emitter.emit('事件名称', 传递参数) -->
    <button @click="emitter.emit('send-toy', toy)">玩具给弟弟</button>
  </div>
</template>

<script setup lang="ts" name="Child1">
import { ref } from "vue";
import emitter from "@/utils/emitter";

// 数据
const toy = ref("奥特曼");
</script>

// 任意组件2: 接收数据
<template>
  <div class="child2">
    <h3>子组件2</h3>
    <h4>电脑：{{ computer }}</h4>
    <h4>哥哥给的玩具：{{ toy }}</h4>
  </div>
</template>

<script setup lang="ts" name="Child2">
import { ref, onUnmounted } from "vue";
import emitter from "@/utils/emitter";

// 数据
const computer = ref("联想");
const toy = ref("");

// 给emitter绑定send-toy事件
emitter.on("send-toy", (value: string) => {
  toy.value = value;
});

// 在组件卸载时解绑send-toy事件
onUnmounted(() => {
  emitter.off("send-toy");
});
</script>
```

- 绑定事件: `emitter.on("事件名称", (参数: 类型) => { //...操作 })`
- 触发事件: `emitter.emit("事件名称", 参数)`
- 解绑事件: `emitter.off("事件名称")`
- 清空所有事件: `emitter.all.clear()`
- 规律:
  - 谁提供数据, 谁触发事件(发布消息)
  - 谁接收数据, 谁绑定事件(订阅消息)
- 需要注意的点:
  - `emiiter.ts` 里面可以定义一些全局都可使用的事件
  - 在任何组件(.vue)中, 只要能摸到`emitter`, 就可以绑定,触发,解绑,甚至清空事件
  - 为了内存考虑, 请务必在组件卸载时, 解绑一些不需要重复利用的事件

> mitt 库相当于给我们提供了一个全局的组件通讯工具

### v-model

> 其实, 实际开发中, 我们很少自己写`v-model`, 但 UI 组件库(Element-Plus)大量使用 v-model 通信

###### 原生的 html 中, `v-model` 实现双向绑定的底层逻辑:

```html
<!-- <input type="text" v-model="username"> -->

<!-- 上面的代码其实是: -->
<input
  type="text"
  :value="username"
  @input="username = ($event.target as HTMLInputElement).value"
/>
```

- `:value` => 实现底层数据到页面的绑定
- `@input` => 实现页面数据到底层的绑定
- `($event.target as HTMLInputElement).value` 是为了符合 ts 语法规范, 类型断言 event.target 就一定是一个 HTMLInputElement(html.input 元素)
- 也可以这样断言: `(<HTMLInputElement>$event.target).value`

###### 组件库里的 v-model 实现双向绑定的底层逻辑(Vue3):

1. 自定义组件 `harry-input`

```ts
<script setup lang="ts">
// 我要先接收一个叫modelValue的参数
defineProps<{
  modelValue: string
}>()
// 再接收一个叫update:modelValue的事件
const emit = defineEmits(['update:modelValue'])
</script>

<template>

  <input type="text"
    // 我在这个原生的input中, 实现原生的双向绑定
    :value="modelValue"
    @input="emit('update:modelValue', ($event.target as HTMLInputElement).value)"
  >
</template>
```

2. 渲染自定义组件时

```html
<!-- <harry-input v-model="username" /> -->

<!-- 上面的代码其实是: -->
<harry-input :modelValue="username" @update:modelValue="username = $event" />
```

- 这个自定义组件绑定了一个属性: `modelValue`
- 同时这个自定义组件绑定了一个自定义事件: `update:modelValue`, 注意这个冒号没有任何其他意义!
- 为什么? `@update:modelValue="username = $event"`
  - 对于原生事件, `$event` 就是事件对象
  - 对于自定义事件, `$event` 就是触发事件时所传递的数据
- **注意**: `modelValue`, `update:modelValue` 关键字都是不能随便改的!
- 如果我非要用其他名称进行修改呢?

  - 使用自定义组件时: `<harry-input v-model:自定义名称="username" />`
  - 自定义组件底层接收时 `defineProps(['自定义名称', '可以收多个'])`, `const emit = defineEmits(['update:自定义名称', 'update:update不能少可以自定义多个'])`

  > 实际投入使用的场景其实是为了可以实现多个参数的传递

### $attrs: 祖孙通信

- 首先了解`v-bind`: `<Child :a="a" :b="b"/>` 等价于 `<Child v-bind="{a:a, b:b}">`, 也就是说, `v-bind可以传一个对象`
- 然后了解`$attrs`: 当子组件没有用`defineProps()`对父组件的传过来的数据进行接收时, 父组件的传递过来的数据其实是存在`$attrs`中的
- 因此要实现祖孙通信, 只需要`<GrandChild v-bind="$attrs">`, 这样孙组件(GrandChild)就可以获得父组件(祖组件)的数据, 包括方法
- 示例

```vue
// 父组件
<script setup lang="ts" name="Father">
import Child from "./Child.vue";
import { ref } from "vue";

const a = ref(1);

function updateA(value: number) {
  a.value += value;
}
</script>
<template>
  <Child :a="a" :updateA="updateA" />
</template>

// 子组件: v-bind="$attrs" (爸爸给的我不要,全给它孙子)
<GrandChild v-bind="$attrs" />

// 孙组件: 正常接收
<script setup lang="ts" name="GrandChild">
defineProps(["a", "updateA"]);
</script>
<template>
  <p>{{ a }}</p>
  <button @click="updateA(6)">点我将爷爷那的a更新</button>
</template>
```

> 同理: 实现孙->祖通信, 和父子通信一样, 爷爷交给孙子一个函数, 孙子调取函数修改爷爷的数据即可.

### $refs: 父传子 #parents: 子传父

> 前面我们实现父->子通信, 都是父亲交给儿子数据, 或者父亲交给儿子函数, 儿子调用函数, 修改父亲的数据
> 如何能做到, 在父组件里, 修改子组件的数据呢?

### 在父组件里直接修改子组件的数据

```vue
// 父组件
<template>
  <!-- 先给儿子打上标记 -->
  <Child ref="child" />
  <button @click="changeSonName">点击修改儿子的名字</button>
</template>

<script setup lang="ts">
import { ref } from "vue";

// 获取儿子的实例对象
const child = ref();

// 改名函数
function changeSonName() {
  child.value.name = "newName";
}
</script>

// 子组件
<script setup lang="ts">
import { ref } from "vue";

const name = ref("oldName");

// 暴露出去
defineExpose({ name });
</script>
```

1. 子组件一定要暴露出去, 父组件才能获取子组件内定义的数据
2. 父组件通过`ref`给子组件打标记, 就可以获取子组件的实例
3. 获取子组件的实例, 就可以获取和修改实例里声明暴露的属性, 方法等
4. **重点**: `$refs` 就是获取当前组件所有被打上标记的子组件:

```vue
<template>
  <!-- 先给儿子打上标记 -->
  <Child ref="child" />
  <Child2 ref="child2">
  <button @click="getAllSon($refs)">点击获取所有子组件的ref</button>
</template>

<script setup lang="ts">
  function getAllSon(refs) {
    for (const key in refs) {
      console.log(refs[key]) // 遍历refs, 摸到每一个儿子
    }
  }
</script>
```

5. 满足 ts 的类型检查

```ts
// 引用 vue提供的接口 ComponentPublicInstance
import type { ComponentPublicInstance } from "vue";

// 继承接口, 并利用接口可复写的特性, 添加属性
interface ChildComponent extends ComponentPublicInstance {
  // 比如我们要用到子组件里的book
  book: number;
}

// 这样就可以通过ts的类型检查 (refs: Record<string, ChildComponent>)
function getAllChild(refs: Record<string, ChildComponent>) {
  for (const key in refs) {
    refs[key].book += 3;
  }
}
```

### 在子组件里直接修改父组件的数据

1. 同理, 父组件必须要先暴露属性 `defineExpose({'要暴露的属性'})`
2. 子组件: 通过`$parent`获取父组件

```vue
<template>
  <button @click="getFather($parent)">获取父亲</button>
</template>

<script setup lang="ts">
function minusHouse(parent) {
  console.log(parent);
}
</script>
```

- 总结: 需要其他组件修改自己的数据, 必须先通过`defineExpose()`暴露
- 父亲获取儿子: `$refs`(一个父亲可能有多个儿子, 复数), 儿子获取父亲: `$parents`(一个儿子只能有一个父亲, 单数)

### provide & inject

> 前面祖孙通信, 还是需要子组件作为父组件和孙组件的桥梁, `v-bind="$attrs"`, 有一种更直接的方式, 不用通过中间的组件

- 示例

```vue
// 祖先
<script setup lang="ts" name="Father">
// 需要引入provide
import { ref, reactive, provide } from "vue";

// 定义数据
const money = ref(100);
const car = reactive({
  brand: "奔驰",
  price: 100,
});

// 定义函数
function updateMoney(value: number) {
  money.value -= value;
}

// 向后代提供 provide
provide("moneyContext", { money, updateMoney });
provide("car", car);
</script>

// 后代
<script setup lang="ts" name="GrandChild">
// 需要引入inject
import { inject } from "vue";

// 解构获取值
const { money, updateMoney } = inject("moneyContext", {
  money: 0,
  updateMoney: (param: number) => {},
});
// 或者直接获取数据
const car = inject("car", { brand: "未知", price: 0 });
</script>
```

1. 提供数据, 导入并使用 `provide('key', value)`
2. 注入数据, 导入并使用 `inject('key', 默认值)`
3. 注意祖先提供数据`moneyContext`时, 里面提供了一个函数
4. 所以通过这个函数, 也可以实现后代操纵祖先的数据

# 插槽

> 插槽其实也是**组件通信**的方式: 子组件声明占位符 slot, 使用子组件时填充占位符

### 默认插槽

```vue
// 子组件
<template>
  <div class="category">
    <!-- 在这里声明占位符 -->
    <slot>默认内容</slot>
  </div>
</template>

// 父组件
<template>
    <div class="content">
      <Category>
        <p>我将填充slot</p>
      </Category>
</template>
```

1. 声明默认插槽: `<slot>默认值, 当没有东西过来时显示</slot>`
2. 填充默认插槽: `<子组件>填充默认插槽的内容, 相当于这里面的东西将会写在<slot></slot>里</子组件>`, 注意:
   - 挂载子组件时必须使用: `<子组件></子组件>` 这样的闭合标签
   - 填充内容可以有默认值, 就在子组件的: `<slot></slot>` 里写

### 具名插槽

> 具有名字的插槽

1. 声明插槽时, 指定 name : `<slot name="main"></slot>`
2. 填充插槽时, 必须使用`<template v-slot:name>包裹你要填充的内容</template>` => 通过 `v-slot:...` 指定要填充的插槽名称
3. 简写 v-slot: `<template #slotName></template>` => `v-slot:` === `#`
4. 默认插槽其实是一个名称叫做 default 的插槽: `<template #default></template>`

### 作用域插槽

> 数据在子组件, 但根据数据生成的结构, 却由父亲决定时, 就需要用到作用域插槽

- 子组件向外提供插槽的同时提供数据: `<slot :key="value" :key2="value2"></slot>`(可以提供多个)
- 父组件接收数据, 并且以不同的样式填充插槽

```html
<子组件>
 <!-- 需要用template包裹, 并且用v-slot="params"接收 -->
 <template v-slot="params">
   <!-- 可以p渲染 -->
   <p>{{ params.key }}</p>
   <!-- 也可以h1渲染 -->
   <h1>{{ params.key2 }}</h1>
   <!-- 如果传过来的是数组, 还可以遍历 -->
   <ul>
    <li v-for="item in params.key3" :key="item.id">{{ item.name }}</li>
   </ul>
 </template>
</子组件>
```

- 既是作用域插槽, 又是具名插槽, 父组件中, 我应该怎么做, 才能既指定填充的插槽, 又获取定义在子组件里的数据呢?
- `<template #slotName="子组件提供的数据">`, 比如`<template #default="scope">内容</template>` => 获取子组件提供的数据存在 scope 中, 并且将内容插入到默认插槽中(myErp 这样做过, 现在知道原理了)

### 组件通信总结

###### 父传子

1. props
2. v-model
3. $refs
4. 默认插槽, 具名插槽

###### 子传父

1. props(父亲给方法)
2. 自定义事件
3. v-model
4. $parent
5. 作用域插槽

###### 祖孙通信

1. $attrs
2. provide, inject

###### 任意组件通信

> 借助第三方库

1. mitt(全局事件定义)
2. pinia(全局状态管理)

# 其他 api

###### 浅层响应式数据

> `shallowRef` & `shallowReactive`: 绕开深度响应, 只能修改浅层的数据, 主要作用场景是避免对每一个属性都做响应式所带来的性能成本提高.

- 通俗点来说, 一个`obj`, 我只关注它的`obj.a`, 不关心它的`obj.b.c.d`, 就可以是浅层定义, 但注意:
  - `shallowRef` 只能摸到 `.value`
  - `shallowReactive` 只能摸到 `.第一层属性`

###### 只读

> 创建一个响应式数据的只读副本, 主要实现对响应式数据的保护和备份

- `const backupState = readonly(ref/reactive定义的响应式数据变量)`
- 此时`backupState`不可编辑和修改
- 浅层只读: `shallowReadonly(某个响应式数据)`, 只有第一层被保护, 更深层就可以随便改了.

###### toRaw & markRaw

1. toRaw: 将响应式数据变为原始数据: `const originData = toRaw(ref/reactive定义的响应式数据)`, 主要作用场景: 将数据交给非 vue 库或者外部时使用, 确保外部收到的是普通数据而不是响应式的
2. markRaw: 标记原始数据, 使其永远不能被转换为响应式: `const car = markRaw({brand:'奔驰', price:100})`后, `const refCar = ref(car)`将失效, `refCar`也不是响应式的

###### customRef 自定义 ref

- **重点**: 如何创建一个自定义的响应式数据

```ts
import { customRef } from 'vue'

// 1, 定义初始值
let initValue = '初始值'
// 2, 定义自定义响应式数据
const myState = customRef((track, trigger) => {
  get() {
    // 2.1, 一定要告诉vue: 请追踪数据
    track()
    return initValue
  },

  set(value) {
    // 当然可以实现更复杂的逻辑
    initValue = value
    // 2.2, 一定要告诉vue: 修改了数据
    trigger()
  }
})
```

- 难点就在于`track()`和`trigger()`这两个 Vue 底层实现的函数, 如果要使用自定义数据, 就要弄明白这两个函数在干嘛?
- `track()`在告诉 vue: myState 这个数据很重要, 请你对它进行持续关注, 当它发生改变时, 请立刻渲染
- `trigger()`在告诉 vue: myState 已经被发生改变了

###### teleport

> teleport 传送门标签可以将其包裹的内容传送到指定区域, "脱离父组件的掌控"

- 使用 `<teleport to='容器'>内容</teleport>` 包裹内容, 可以将内容塞到指定位置
- 常见的使用场景就是弹窗, 要求弹窗务必以视口定位, 可以使用

```vue
<style scoped>
.modal {
  width: 600px;
  height: 400px;
  /* 根据视口定位 */
  position: fixed;
  /* 左边偏移50% */
  left: 50%;
  /* 一定更减去自己的一半才是正中间 */
  margin: 100px 0 0 -300px;
}
</style>
```

- 但如果此时其父容器添加如滤镜类的样式, 会导致其`position: fixed;`失效
- 此时我们就必须要: 使用 teleport 把这个组件直接塞到 body 里, 而不是继续留在父容器中(让它的父容器变成 index.html 里的 body 部分)

```html
<teleport to="body">
  <div class="modal"></div>
</teleport>
```

###### Suspense

> 实验性 api, 了解即可

- 主要作用场景: 子组件有异步任务时

```vue
<template>
  <Suspense>
    <template #default>
      <!-- child 是一个有异步任务的组件 -->
      <Child />
    </template>

    <template #fallback>
      <p>加载ing...</p>
    </template>
  </Suspense>
</template>
<sciprt setup lant="ts">
  import { Suspense } from 'vue'
</sciprt>
```

- 当 default 插槽里的 Child 没加载出来时, 加载 fallback 插槽里的内容

###### main.ts 里的其他 api

1. 在 `main.ts`中注册全局组件, 然后在任何其他地方, 都可以直接挂载`<已注册的全局组件>`, 不用再`import`

```ts
// ...
import TestApp from "./views/Testapp.vue";

const app = createApp(App);

// 注册全局组件 app.component('组件名', 组件)
app.component("TestApp", TestApp);

app.mount("#app");
```

2. `app.config.globalProperties.x = 100`: 定义全局变量, 任何地方都可以 `{{x}}`, 展示 100
3. `app.directive`: 定义全局指令

```ts
// 定义全局指令 mark, 它绑定的元素拥有样式: color:red
app.directive('mark', (element) => {
  element.style.color = 'red'
})

// 然后在其他地方使用,此时: 123是红色
<p v-mark>123</p>
```

- 还有`app.mount()`挂载组件, `app.unmount()`卸载组件, `app.use()`使用插件

> Vue2 都是 `Vue.xxx`, 现在是 `const app = createApp(App)`, 然后`app.xxx`了
