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

### 定义和使用的 demo

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
