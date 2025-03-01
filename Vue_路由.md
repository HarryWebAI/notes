# 创建 VueRouter 项目

- 用于定义 url 规则与具体 view 的映射关系,在单页中实现数据的更新
- 在已存在但没有选择 VueRouter 的项目中安装`npm install vue-router@latest --save`
- 或者使用 Vite 新建 Vue 项目时勾选支持 VueRouter`npm create vue@latest`
  - 输入名称啦,是否支持 ts,jsx 啦...省略
  - 是否引**入 Vue Router**进行单页应用开发?**是!**
  - ...剩下也略
- 创建完成后需要:
  - `npm install` 根据依赖安装包
  - `npm run format` 开启代码格式化
  - `npm run dev` 启动项目
- 和以前不同,这个项目多了
  - `~/src/router/`以及其内部的`index.js`
  - `~/src/views`以及里面自带的两个.vue 视图
    > 说到底, Vue 是一个 web 单页应用开发框架`Single Page Application`,其最直观的感受就是,浏览器始终局部更新,而非整体跳转,页面重复的部分不会因为地址改变而改变

# 详解默认项目

### main.js 和 App.vue

- `~/src/main.js`中

```javascript
import "./assets/main.css";

import { createApp } from "vue";
import App from "./App.vue";
// 导入router
import router from "./router";

const app = createApp(App);

// 使用router
app.use(router);

app.mount("#app");
```

- 重点就是注释的那两句,导入 router 并声明使用
- 然后在`App.vue`中

```vue
<script setup>
import { RouterLink, RouterView } from "vue-router";
// 后面略
</script>

<template>
    <div>
      <!-- 略 -->
      <nav>
        <!-- 路由导航 -->
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div>
  </header>
  <!-- 路由出口 -->
  <RouterView />
</template>

<style scoped>
/* 略 */
</style>
```

- 先从`vue-router`中导入了两个组件`{RouterLink, RouterView}`,即路由链接和路由出口
- RouterLink 用来挂载跳转的链接,RouterVie 用来接受指令后渲染不同的组件
- RouterLink 就像传统的 html.a 标签一样它 to 的地址也就是 a.href 的地址
- RouterView 则像一个子窗口,RouterLink 切换不同的路由,它接收指令,渲染不同的内容
- 组件就在`~/src/vies/`里面

### 重写 index.js

- `~/src/router/index.js`就是路由配置文件,重写它

```javascript
// 导包
import {
  createRouter, // 必须导入,用于创建路由对象
  createWebHistory, // 二选一:选用html形式的路由 "localhost/路由"
  createWebHashHistory, //二选一:选择hash形式的路由 "localhost/#/路由"
} from "vue-router";

// 开始创建路由对象
const router = createRouter({
  // 第一参数,history:指定路由形式
  history: createWebHashHistory(),

  // 第二参数,routes:[{指定路由1}, {指定路由2}, {指定路由3}, ...]
  routes: [
    // 配置根路径,也就是home路径
    {
      path: "/",
      name: "home",
      component: import("../views/HomeView.vue"), //懒加载
    },

    // 配置About路径
    {
      path: "/about",
      name: "about",
      component: import("../views/AboutView.vue"),
    },

    // 其他路由同理
  ],
});

// !必须要暴露router
export default router;
```

- 为什么要用 hash 历史?因为有可能我们的项目是前后端在一台服务器,一个域名上的.通常后端采用传统 url,前端如果也采用,路由就容易写重.
- 上面的代码中我们写路由时,`component: import("../views/AboutView.vue"),`是一种懒加载的方式,原本的文件给我们展示了传统方法

```js
...
import HomeView from "../views/HomeView.vue"; //先导入homeview视图
...

routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView, //这种是正常加载,先在上面导入,再在这里直接加载
    },
    ...,
]
```

- 最后一定要记得暴露 router,这样才能在 main.js 中使用

# 路由嵌套

- 举例:App.vue 有导航"新闻"至新闻组件.

  - 新闻下有子组件"新闻详情",和"新建新闻"

- 实现:`App.vue`

```vue
<RouterLink to="/news">新闻</RouterLink>
```

- 新闻组件:`NewsView.vue`

```vue
<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <ul>
    <li>
      <!-- 有两种指定路由的方式 -->
      <!-- 一种就是 to="写好的路由" -->
      <RouterLink to="/news/detail">新闻1</RouterLink>
      <!-- 另外一种是 :to="{name:'路由名称'}" -->
      <RouterLink :to="{ name: 'news_detail' }">新闻2</RouterLink>
      <RouterLink :to="{ name: 'news_create' }">新闻发布页</RouterLink>
    </li>
  </ul>

  <!-- 必须要有路由出口 -->
  <RouterView></RouterView>
</template>
```

- `NewsDetailView.vue`和`NewsCreateView.vue`是假的,略

- **重点**:路由配置文件`index.js`

```js
...
    // 嵌套路由
    {
      path: '/news',
      name: 'news',
      component: import('../views/news/NewsView.vue'),

      // 子路由
      children: [
        {
          path: 'detail',
          name: 'news_detail',
          component: import('../views/news/NewsDetailView.vue'),
        },
        {
          path: 'create',
          name: 'news_create',
          component: import('../views/news/NewCreateView.vue'),
        },
      ],
    },
...
```

> 子路由的 path 不加`/`

# 路由传参

### 两种传递方式

- `/value`,需要在路由部分声明`path: 'detail/:id',`

- `?key=value`,不需要再路由部分声明

### 具体传递方式

- `/value`

```vue
<RouterLink to="/news/detail/1">新闻1</RouterLink>
<RouterLink :to="{ name: 'news_detail', params: { pk: 2 } }">新闻2</RouterLink>
```

- `?key=value`

```vue
<RouterLink
  :to="{ name: 'news_create', query: { tag: '通过 问号key=value 的形式传参' } }"
>新闻发布页</RouterLink>
<RouterLink to="/news/create?tag=哈哈哈">新闻发布</RouterLink>
```

### 子组件接收处理

- `/`

```vue
<script setup>
// 1,获取参数,先导包
import { onMounted, onUpdated } from "vue";
import { useRoute } from "vue-router";

// 2,实例化
const route = useRoute();

// 3,在onMounted,组件挂载完成后接收参数
let pk; //用于存储参数
onMounted(() => {
  pk = route.params.pk;
});

// 4,以及在onUpdated,组件发生更新时更新参数
onUpdated(() => {
  pk = route.params.pk;
});
</script>
```

- `?`

```vue
<script setup>
import { useRoute } from "vue-router";
import { onMounted } from "vue";

const route = useRoute();

let tag;

onMounted(() => {
  tag = route.query.tag;
});
</script>
```

- 总结:就是通过 `router.` + `?`就用`query.` | `/`就用`params.` + `参数名称`

# 编程式导航

- 整个按钮`<button @click="pushNewsDetail">点击跳转到新闻详情页</button>`

- 完成这个按钮的方法

```js
import { useRouter } from "vue-router";

const router = useRouter();

const pushNewsDetail = () => {
  // push 有历史记录
  // router.push('/news/detail/3')
  // router.push({ name: 'news_detail', params: { pk: 3 } })
  // replace 没有历史记录(浏览器不会记住上一个页面)
  router.replace("/news/detail/3");
};
```

- `push`和`replace`的最大区别就是给不给浏览器存入历史记录
- `route`和`router`的最大区别就是,前者用来接收参数,后者用来跳转页面

# 导航守卫

### 全局守卫

- 当前项目下全局生效,写在`index.js`中
- `beforeEach((to, from) => {//相关逻辑})` => 在当前项目下,去任何路由前,都需要...
- `afterEach((to, from) => {//相关逻辑})` => 在当前项目下,访问任何路由后,都需要...

### 组件守卫

- 当前组件下生效,写在某个组件中
- `onBeforeRouteLeave((to, from) => {//相关逻辑})` => 在当前组件下,离开本路由,需要做...
- `onBeforeRouteUpdate((to, from) => {//相关逻辑})` => 更细本组件,需要做...

### 路由导航守卫

- 写在`index.js`中的路由配置里面

```js
...
    {
      path: '/about',
      name: 'about',
      component: import('../views/AboutView.vue'),
      // 在访问本路由前,需要....
      beforeEnter: (to, from) => {
        // 需要确认是否登录?...
      },
    },
```

# 路由元信息

- 在路由配置文件`index.js`中,配置路由时,指定`meta`属性,相当于给路由添加了附加信息,比如`meta: {reruireAuth: true}`声明该路由需要登录才可以访问
- 完善验证逻辑:

```js
router.beforeEach((to, from) => {
  // 如果路由要求登录, 并且用户没有登录, 并且要去的路由不是login
  if (to.mata.requireAuth == true && !isAuthenticated && to.name != "login") {
    // 那么去登录路由
    return { name: "login" };
  }
});
```
