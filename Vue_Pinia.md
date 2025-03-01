# 先把 vsCode 搞定

- `vueinit` 每次创建的都是 vue2 的模板
- `Ctrl + Shift + p`打开命令面板,输入`Preferences: Configure User Snippets`
- 搜索或者新建 vue.json,内容如下

```
{
	"Vue 3 Component": {
    "prefix": "vueinit",
    "body": [
      "<script setup>",
      "</script>",
      "",
      "<template>",
      "</template>",
      "",
      "<style scoped>",
      "</style>"
    ],
    "description": "Basic Vue 3 Component Template"
  }
}
```

- 意思是当我们输入`vueinit`时,自动给我们一个 vue3 模板

# 创建 Pinia

- 什么是 Pinia:全局变量管理器,比如用户状态管理

### 安装

- `npm instal pinia@latest`

### 创建 Vue 时声明引用 Pinia

- `npm create vue@latest`
  - 略
  - 引用 Pinia, **是**

### 检查项目文件夹

- 发现多了`~/src/stores/`以及里面的`counter.js`

```js
import { ref, computed } from "vue";
import { defineStore } from "pinia";

export const useCounterStore = defineStore("counter", () => {
  // 定义了一个响应式变量
  const count = ref(0);
  // 定义了一个计算属性
  const doubleCount = computed(() => count.value * 2);
  // 定义了一个方法
  function increment() {
    count.value++;
  }
  // 当然我们还可以自己定义更多的变量,属性,方法

  // 最后必须返回定义的 {变量, 计算属性, 方法}
  return { count, doubleCount, increment };
});
```

> 这个 counter.js 就是一个示例,接下来我们访问它

# 在组件中使用 Pinia 为我们定义的全局数据

```vue
<script setup>
// 导入
import { useCounterStore } from "@/stores/counter";

// 创建对象
const counterStore = useCounterStore();

// 假设我们有个按钮调用下面的方法,更新全局变量count
const updateCount = () => {
  // 1,直接更新
  // counterStore.count++
  // 2,批量更新
  // counterStore.$patch({ count: counterStore.count + 1 })
  // 3,通过方法更新
  counterStore.increment();
};
</script>
```

# 简单实现用户状态功能

- 新建`~/src/store/user.js`

```js
// 导包
import { defineStore } from "pinia";
import { ref } from "vue";

// 必须这么写 export const use什么什么 = defineStore('名称', () => { //定义的变量,方法,计算属性等... })
export const useUserStore = defineStore("user", () => {
  // 定义一个用户,名称和邮件默认为空,默认为未激活
  let user = ref({
    username: "",
    email: "",
    is_active: false,
  });

  // 定义一个函数,用于设置user的值
  const setUser = (user_obj) => {
    // 将传入的对象赋值给user
    user.value = user_obj;
    // 并将数组存储在本地浏览器中, key也叫user, value转成JSON字符串的user_obj对象
    localStorage.setItem("user", JSON.stringify(user_obj));
  };

  // 必须要return出去才能在其他组件使用
  return { user, setUser };
});
```

- 然后我们在`LoginView.vue`中模拟登录,就是导包后`userStore = useUserStore()`后,`userStore.setUser(假数据)`传进去
- 然后再访问`ProfileView.vue`,同样通过`userStore = useUserStore()`,就可以访问到同时存储在 Pinia 和浏览器本地存储中的`user`的信息了
