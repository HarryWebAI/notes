# React 是什么?

- 构建 WEB 和原生交互页面的库

### 优势

- 较传统 DOM(纯 html+css+js): 组件化开发, 性能更好
- 较其他库(Vue...): 生态更丰富, 跨平台支持

### 创建 React

- `npx create react-app <app-name>`(已弃用)
- `npm create vite@latest my-app ` => 使用 vite 构建
  - 选择 react
  - 选择 typescript + swc(Speedy Web Compiler => rust 写的用于快速编译 ts 的工具)
  - 进入目录, `npm install`, `npm run dev` => 安装依赖, 运行进入开发环境

### 项目目录(需要重点关注的)

- `/node_modules/` => node 库
- `/public/` => 公开文件
- `/src/` => **源码**: 我们工作的地方
  - `/assets/` => 静态文件(图片, 图标, css)
  - `App.tsx` => 项目主文件(类似于 Vue, App.vue)
  - `main.tsx` => 项目注文件(类似于 Vue, main.js)
- `package.json` => 依赖声明

### 项目是如何跑起来的? App.tsx 和 main.tsx

```tsx
/** App.tsx */
// 项目根组件
function App() {
  return (
    <>
      <div>
        <h1>Hello World</h1>
      </div>
    </>
  );
}

export default App;

/** main.tsx */
// 导入核心依赖
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";

// 导入根组件
import App from "./App.tsx";

// 渲染根组件到id为 <root> 的Dom元素上
createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

# jsx

> jsx 是 javascript + xml 的缩写, 实现在 js 代码中编写 html 模板结构, 也是**React 中编写 UI 模板的方式**

- 代码示例

```ts
const message: string = "hello world!";

const count: number = 10;

const getName = (): string => {
  return "张三";
};

const list: Array<{ id: number; name: string }> = [
  { id: 1, name: "vue" },
  { id: 2, name: "react" },
  { id: 3, name: "angular" },
];

const isLogin: boolean = false;

const articleType = 2;

const getArticleTemplate = (type: number) => {
  if (type === 1) {
    return <div>无图</div>;
  } else if (type === 2) {
    return <div>单图</div>;
  } else if (type === 3) {
    return <div>多图</div>;
  }
};

const handleClick = (e: object) => {
  alert(`按钮被点击了, 事件对象: ${e}`);
  console.log(e);
};

const handleClickWithParams = (num: number, e: object) => {
  alert(`按钮被点击了,  传进来的参数是: ${num}, 事件对象: ${e}`);
  console.log(num);
  console.log(e);
};

function App() {
  return (
    <>
      <div>
        {/* 简单示例 */}
        <p>{message}</p>

        {/* 使用引号传递字符串 */}
        <p>"使用引号传递字符串"</p>

        {/* 识别js变量 */}
        <p>{count}</p>

        {/* 函数调用 */}
        <p>{getName()}</p>

        {/* 传递js对象 */}
        <div style={{ color: "red" }}>123</div>

        {/* 遍历列表 */}
        <ul>
          {/* 列表.map((item) => (<标签 key={item.id}>{内容}</标签>)) */}
          {list.map((item) => (
            // 重复的元素应该有个key, 通常用id
            <li key={item.id}>{item.name}</li>
          ))}
        </ul>

        {/* 条件渲染 */}
        <div>
          {/* 条件 ? 结果1 : 结果2 */}
          {count > 10 ? <span>大于10</span> : <span>小于10</span>}
          <br />
          {/* 条件 && 结果 => && 前面为true, 显示后面的内容 */}
          {isLogin && <span>1</span>}
          <br />
          {/* 条件 || 结果 => || 前面为false, 显示后面的内容 */}
          {isLogin || <span>2</span>}
        </div>
        {/* 复杂条件渲染: 定义函数 */}
        {getArticleTemplate(articleType)}

        {/* 事件绑定 */}
        <button onClick={handleClick}>点击</button>
        {/* 事件绑定: 传参的同时获取事件对象 */}
        <button onClick={(e) => handleClickWithParams(1, e)}>点击</button>
      </div>
    </>
  );
}

export default App;
```

- 渲染普通的字符串: `""`
- 渲染普通的变量, 函数: `{变量/函数}`
- 遍历列表: `{列表.map((item)=>({内容}))}`
- 简单条件判断: `表达式 ? 表达式为真 : 表达式为假`, 或者
  - `表达式 && 为真显示`
  - `表达式 || 为假显示`
- 复杂条件判断: 通过自定义一个函数, 函数内部根据传进来的参数, return 出去不同的内容
- 事件(点击)绑定: `<标签 onClick={点击后触发的函数}>...</标签>`
- 事件(点击)绑定时, 既传递参数, 顺带把被点击的的元素对象传过去`<标签 onClick(对象)={点击后触发的函数(参数, 对象)}>...</标签>`
- jsx 并不是标准的 js 语法, 它是**js 的语法扩展**, 浏览器本身不能识别, 需要**解析工具(babel, swc...)**解析后才能在**浏览器**运行(在 js 里面写标签 -> 解析器解析 -> 浏览器认识)

# 组件

> 一个组件就是用户界面的一部分, 它可以有自己的逻辑和外观, 组件之间可以互相嵌套, 也可以复用多次, 总体来说, 组件化开发可以让开发者像"搭积木"一样构建前端页面

- 在 React 中, 一个组件就是首字母大写的函数, 内部存放了组件的逻辑和视图 UI, 渲染组件只需要把组件当成标签书写即可

```ts
// 1,定义组件
function MyButton() {
  return <button>点击</button>;
}

// 2, 也可以用箭头函数的形式定义组件
const MyH1 = () => {
  return <h1>箭头函数也可以</h1>;
};

function App() {
  return (
    <div>
      {/* 3,渲染组件 */}
      <MyButton></MyButton>
      <MyH1></MyH1>
    </div>
  );
}

// 暴露出去, 在main.tsx中导入并在根组件上以标签<App />的形式渲染
export default App;
```

# useState

> useState 本质上是一个 React Hook, 它允许我们给组件添加一个**状态变量**, 从而控制影响组件的渲染结果

> 说人话: `React.useState == Vue.ref`

```ts
// 导入 useState
import { useState } from "react";

function App() {
  // const [变量, 设置变量的函数] = useState(初始值)
  const [count, setCount] = useState(0);

  // 也可以接收一个复杂的数据类型: 对象
  const [form, setForm] = useState({
    name: "张三",
  });

  const handleClick = function (): void {
    // 作用1: 修改count值
    // 作用2: 触发组件的重新渲染
    setCount(count + 1);
  };

  const handleChange = function (): void {
    setForm({
      // ...展开运算符: 将form对象中的所有属性都复制到新的对象中
      ...form,
      name: form.name === "张三" ? "李四" : "张三",
    });
  };

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={handleClick}>点击+1</button>
      <h1>{form.name}</h1>
      <button onClick={handleChange}>修改名称</button>
    </div>
  );
}

export default App;
```

- 定义一个 useState 变量: `const [状态变量名, 函数] = useState(初始值)`
- `...对象`: 展开运算符, 将 form 对象中的所有属性都复制到新的对象中
- 在 React 中, 状态被认为是只读的, 我们应该始终**替换它而不是修改它**, 因为直接修改不能引发视图更新(说人话: 必须要用函数修改状态变量, 而不是直接给变量赋新值)

```ts
// 错误示范
const handleClick = function () {
  count++;
};
```

# 样式

### 组件基础样式方案

- 新建一个`@/index.css`样式表文件
- 导入并使用

```ts
import "./index.css";

function App() {
  return (
    <div>
      {/* 行内样式控制 */}
      <h1 style={{ color: "red", fontSize: "50px" }}>Hello World</h1>

      {/* 类名控制 */}
      <h1 className="title">Hello World</h1>
    </div>
  );
}

export default App;
```

- 可以直接`style={{}}` (行内)
- 也可以导入`.css`文件, 在标签上通过`<标签 className="样式表里面声明的样式类">...</标签>` (类名)

### 一个小案例

```ts
// 导入 useState, 和样式表(active的按钮字体是红色)
import { useState } from "react";
import "./index.css";

// ts: 定义类型 Comment, 是一个对象, 格式如下
type Comment = {
  id: number;
  name: string;
  content: string;
};

function App() {
  // 定义评论列表, 是一个由 Comment 这种格式的对象组成的数组, 默认为空数组
  const [comments, setComments] = useState<Comment[]>([]);

  // 1, 发表评论
  const handleSubmit = function () {
    // 1.1, 通过id找到元素, 再获取数据
    const commenter = document.getElementById("commenter") as HTMLInputElement;
    const commentContent = document.getElementById(
      "commentContent"
    ) as HTMLTextAreaElement;

    // 1.2, 简单的数据验证: 不能为空
    if (commenter.value === "" || commentContent.value === "") {
      alert("请输入评论者或评论内容");
      return;
    }

    // 1.3, 整理数据
    const newComment = {
      id: comments.length + 1,
      name: commenter.value,
      content: commentContent.value,
    };

    // 1.4, 更新 comments 变量的状态实现页面动态渲染
    setComments([...comments, newComment]);

    // 1.5, 清空输入框
    commenter.value = "";
    commentContent.value = "";
  };

  // 2, 删除评论, 传入评论的id
  const handleDelete = function (id: number) {
    // 使用.filter筛选数组, 只留下没有被删除的评论
    setComments(comments.filter((comment) => comment.id !== id));
  };

  // 3, 切换样式和删除
  // 3.1, 先定义两个tab, 存进tabs
  const tabs = [
    {
      id: 1,
      name: "默认",
    },
    {
      id: 2,
      name: "最新",
    },
  ];

  // 3.2, 定义一个 useState 用于记录当前被选中的tab, 同时配置默认被选中的tab为 tabs数组里的第一个元素
  const [currentTab, setCurrentTab] = useState(tabs[0]);

  // 3.3, 激活样式和排序, 传入的参数是tab.id
  const handleSort = function (id: number) {
    // 3.3.1, 首先, 通过tab.id找到指定tab, 设置激活的(拥有active样式)的tab为当前被点击的tab
    setCurrentTab(tabs.find((tab) => tab.id === id) || tabs[0]);

    // 3.3.2, 通过传入的tab.id, 更新数组的排序规则
    if (id === 1) {
      // Array.sort() 实现排序
      setComments(comments.sort((a, b) => a.id - b.id));
    }
    if (id === 2) {
      setComments(comments.sort((a, b) => b.id - a.id));
    }
  };

  return (
    <div>
      {/* 标题, 排序按钮 */}
      <div>
        <h1>评论列表</h1>
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => handleSort(tab.id)}
            // 谁被选中, 谁有拥有active这个样式类, 实现激活样式
            className={currentTab.id === tab.id ? "active" : ""}
          >
            {tab.name}
          </button>
        ))}
      </div>

      {/* 评论列表 */}
      <div>
        <ul>
          // 遍历评论列表
          {comments.map((comment) => (
            <li key={comment.id}>
              <h3>{comment.name}</h3>
              <p>{comment.content}</p>
              // 删除按钮传参: 评论的id
              <button onClick={() => handleDelete(comment.id)}>删除</button>
            </li>
          ))}
        </ul>
      </div>

      {/* 发表评论 */}
      <div>
        <input
          type="text"
          name="commenter"
          id="commenter"
          placeholder="请输入评论者"
        />

        <br />
        <br />
        <textarea
          name="commentContent"
          id="commentContent"
          placeholder="请输入评论内容"
        ></textarea>

        <br />
        <br />
        <button onClick={handleSubmit}>发表评论</button>
      </div>
    </div>
  );
}

export default App;
```

- 新增评论:
  1. 创建两个输入框配上 id, 再加一个绑定点击事件的按钮
  2. 按钮的点击事件实现: 通过 id 获取输入框里的内容(as HTMLTextAreaElement)
  3. 更新 comments
- 删除评论
  1. 遍历时, 多遍历出来一个删除按钮, 绑定点击事件, 传入参数为被遍历的那条评论的 id
  2. 通过`Array.filter()`筛选更新, 通过筛选排除掉 id 为传入参数的那条评论数据
- 样式切换

  1. 用列表定义一组按钮的编号和名称
  2. 渲染在页面上
  3. 定义一个 useState 数据用于记录当前被激活的按钮, 默认为样式列表第一项
  4. 判断样式, 当按钮的 id 和当前被激活按钮的 id 相等时, 当前按钮就拥有激活样式
  5. 根据传入的 tab.id, 来判断排序的规则, 进行排序

### 补充: 使用`lodash`进行排序

1. 安装: `npm i --save-dev @types/lodash`
2. 使用

```ts
// 导入 lodash
import _ from "lodash";

// 激活样式和排序
const handleSort = function (id: number) {
  setCurrentTab(tabs.find((tab) => tab.id === id) || tabs[0]);
  if (id === 1) {
    // 使用 _.orderBy(要排序的列表, "根据哪个字段进行排序", "asc正序/desc倒序")
    setComments(_.orderBy(comments, "id", "asc"));
  }
  if (id === 2) {
    setComments(_.orderBy(comments, "id", "desc"));
  }
};
```

### 补充: 使用`classNames`优化类名控制

1. 安装: `npm install classnames --save`
2. 使用

```ts
// 导入
import classNames from "classnames";

<button
  key={tab.id}
  onClick={() => handleSort(tab.id)}
  // 在这里使用 className = {classNames({样式类名: 判断条件})}
  className={classNames({
    active: currentTab.id === tab.id,
  })}
>
  {tab.name}
</button>;
```

### 补充: 受控表单绑定

> `v-model`

- 设置一个 `useState` 变量
- 通过`value={变量名}`绑定变量
- 通过`onChange={(e) => 函数(e.target.value)}`绑定变量设设置变量的函数

```ts
// 评论者
const [commenter, setCommenter] = useState("");
// 评论内容
const [commentContent, setCommentContent] = useState("");

// 添加数据...
const newComment = {
  id: comments.length + 1,
  // 评论者
  name: commenter,
  // 评论内容
  content: commentContent,
};

{
  /* 发表评论 */
}
<div>
  <input
    placeholder="请输入评论者"
    onChange={(e) => setCommenter(e.target.value)}
    value={commenter}
  />
  <br />
  <br />
  <textarea
    placeholder="请输入评论内容"
    onChange={(e) => setCommentContent(e.target.value)}
    value={commentContent}
  ></textarea>
  <br />
  <br />
  <button onClick={handleSubmit}>发表评论</button>
</div>;
```

### 获取 DOM

- 使用`useRef`钩子函数

```ts
// 导入 useRef
import { useRef } from "react";

function App() {
  // 定义 ref
  const inputRef = useRef(null);

  const handleClick = () => {
    console.dir(inputRef.current);
  };

  return (
    <div>
      // 绑定 ref
      <input type="text" ref={inputRef} />
      <button onClick={handleClick}>获取输入框内容</button>
    </div>
  );
}

export default App;
```

- 聚焦 `inputRef.current.focus()`

### 补充: `uuid`和`dayjs`

1. 安装 uuid: `npm install uuid --save`
2. 安装 dayjs: `npm install dayjs --save`
3. 使用:

```ts
// 导入
import { v4 as uuidv4 } from "uuid";
import dayjs from "dayjs";

const comments = [
  {
    // 使用 uuid 生成独一无二的 id
    id: uuidv4(),
    name: "张三",
    content: "评论内容",
    // 使用 dayjs() 生成当前事件, format('设置格式')
    commentTime: dayjs().format("YYYY-MM-DD HH:mm:ss"),
  },
];
```

# 组件通信

> 组件之间的数据传递, 根据组件嵌套关系的不同, 有不同的通信方法

### 父子通信

###### 父传子

```ts
// 子组件
// 接收父组件传递的数据: props: {key: type}
function Son(props: { msg: string }) {
  return <div>{props.msg}</div>;
}

// 父组件
function App() {
  const msg: string = "hello";
  return (
    <div>
      {/* 挂载子组件 */}
      <Son msg={msg} />
    </div>
  );
}

export default App;
```

1. 创建一个子组件`Son`
2. 创建一个父组件`App`
3. 父组件创建一个常量`msg`, 并在挂载子组件时通过`<Son key={常量名}>`
4. 子组件通过配置参数`(props: {key: type})`进行接收, 最后在模板上渲染`{props.key}`即可实现父传子

- **props 可以传递任意数据**
- **但子组件的 props 只读**, 不能直接进行修改, 父组件的数据只能父组件修改
- 一种特殊的情况: 如下所示, 此时, 可以通过`props.children`取到 span

```ts
<Son>
  <span></span>
</Son>
```

###### 子传父

```ts
import { useState } from "react";

// 子组件
// 获取时也可以解构赋值, 不用props, 用结构赋值 ({ onGetMsg }: { onGetMsg: (msg: string) => void })
function Son(props: { onGetMsg: (msg: string) => void }) {
  const sonMsg = "子组件想传递给父组件的信息";
  return (
    <div>
      <button
        onClick={() => {
          // 如果结构赋值, 这里就不用 props.onGetMsg 了
          props.onGetMsg(sonMsg);
        }}
      >
        点击我
      </button>
    </div>
  );
}

function App() {
  const [msg, setMsg] = useState("");

  const getMsg = (msg: string) => {
    setMsg(msg);
  };
  return (
    <div>
      <h1>App</h1>
      <Son onGetMsg={getMsg} />
      <h2>{msg}</h2>
    </div>
  );
}

export default App;
```

1. 父组件搞个函数交给子组件
2. 子组件接收函数(还是父传子)
3. 子组件将自己想要传递的数据通过父组件交给自己的函数传递出去

### 兄弟通信

```ts
import { useState } from "react";

// 子组件1: 哥哥
function BrotherOne({ onGetMsg }: { onGetMsg: (msg: string) => void }) {
  const brotherOneMsg = "哥哥想传递给弟弟的信息";
  return (
    <div>
      <button onClick={() => onGetMsg(brotherOneMsg)}>
        点击我获取哥哥的数据
      </button>
    </div>
  );
}

// 子组件2: 弟弟
function BrotherTwo({ msg }: { msg: string }) {
  return (
    <div>
      <h2>弟弟接收到的数据: {msg}</h2>
    </div>
  );
}

// 父组件
function App() {
  const [msg, setMsg] = useState("");

  const getMsg = (msg: string) => {
    setMsg(msg);
  };

  return (
    <div>
      <BrotherOne onGetMsg={getMsg} />
      <BrotherTwo msg={msg} />
    </div>
  );
}

export default App;
```

1. 子组件 1 传递给父组件
2. 父组件作为桥梁, 将子组件 1 的内容传递给子组件 2
3. 说白了是靠共同父组件 **(状态提升)**

### 跨层组件通信

```ts
import { createContext, useContext } from "react";

const MessageContext = createContext<string>("");

function GrandSon() {
  const msg: string = useContext(MessageContext);
  return (
    <div>
      <h2>我是底层组件!</h2>
      <h2>我获取到了顶层组件传递的数据:</h2>
      <h2>{msg}</h2>
    </div>
  );
}

function Son() {
  return (
    <div>
      <GrandSon />
    </div>
  );
}

function App() {
  const msg = "我是顶层组件的数据";
  return (
    <div>
      <MessageContext.Provider value={msg}>
        <Son />
      </MessageContext.Provider>
    </div>
  );
}

export default App;
```

- 父组件(顶层组件) -> 子组件(中间) -> 孙组件(底层组件), 实现孙组件获取父组件的数据

1. 导入`createContext, useContext`
2. 初始化`const MessageContext = createContext<string>('')` (createContext)
3. 父组件渲染子组件时, 外面包一层`MessageContext.Provider` (Provider 提供)
4. 孙组件(底层组件)跨过子组件获取父组件(顶层组件)的数据: `const msg: string = useContext(MessageContext)` (useContext)

- 注意! 子组件也可以通过这样的方式获取父组件的数据, 因为他俩的相对关系也是父顶, 子底, 但是有更方便的父子通信方式, 所以不用.(可以没必要, 而不是不行)

# useEffect

> useEffect 是一个 React Hook 函数, 用于在 React 组件中创建不是由事件引起, 而是**由渲染本身引起的操作**, 比如发送 Ajax 请求, 更改 DOM 等

### demo

```ts
import { useEffect, useState } from "react";

// 配置接口
const URL = "https://jsonplaceholder.typicode.com/todos";

function App() {
  // 配置接收数据的list
  const [list, setList] = useState<{ id: number; title: string }[]>([]);

  // 数据获取函数
  async function getList() {
    // 请求接口
    const response = await fetch(URL);
    // 转为json
    const result = await response.json();
    // console.log(result);
    // 设置list的值
    setList(result);
  }

  // ** 使用 useEffect 调用数据获取函数
  useEffect(() => {
    getList();
  }, []);

  return (
    <div>
      <h1>列表</h1>
      <ul>
        {list.map((item) => (
          <li key={item.id}>{item.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

1. 导入`useEffect`
2. 配置一个接口地址
3. 声明 useEffect(), 第一个参数传递一个回调函数, 回调函数中定义一个函数请求接口, 然后将值赋给`list`: 模拟请求接口数据, 并由 list 接收
4. `useEffect(()=>{}, [依赖项])` => 通常来说, 第一个参数被称为`副作用操作`, 第二个参数被称为`依赖项数组`

### 依赖项

> `useEffect()`根据第二参数`依赖项`的不同, 决定第一参数`副作用操作`的`执行时机`

1. 空数组: `useEffect(() = >{ }, [])` => **组件初始渲染执行一次**
2. 什么都不写(没有第二参数):`useEffect(() => { })` => **组件初次渲染一次**, **组件更新执行一次**
3. 声明依赖项后: `useEffect(() => { }, [比如某个useState变量])` => **初次渲染一次**, **当依赖项变化时执行一次**

> **不传参和传空数组不一样**

### 清除副作用

```ts
import { useEffect, useState } from "react";

function Son() {
  useEffect(() => {
    const timer = setInterval(() => {
      console.log("定时器执行ing...");
    }, 1000);

    // return 一个匿名箭头函数
    return () => {
      // 组件卸载时执行清除副作用操作的逻辑
      clearInterval(timer);
    };
  }, []);
  return <div>Son</div>;
}

function App() {
  const [show, setShow] = useState(true);
  return (
    <div>
      <button onClick={() => setShow(!show)}>隐藏son</button>
      {show && <Son />}
    </div>
  );
}

export default App;
```

1. Son 组件在被渲染后, 开启定时器
2. App 组件挂载 Son 组件, 按钮点击事件模拟卸载 Son 组件
3. 如果没有在`useEffect()`中 return 出去一个匿名函数, 并且在匿名函数里面`清除副作用`的话, 定时器会在 Son 被卸载后继续执行

# 自定义 hook

### 封装自定义 hook 的思路

1. 声明一个以 use 打头的函数
2. 函数体内编写业务逻辑
3. return 出去组件中用到的状态或者函数(以对象或者数组的形式)
4. 在哪里用, 就解构取出来

```ts
import { useState } from "react";

// 封装自定义hook
function useToggle() {
  // 逻辑
  const [show, setShow] = useState(true);

  const toggle = () => {
    setShow(!show);
  };

  // return 出去
  return { show, toggle };
}

function App() {
  // 解构赋值获取状态数据和函数
  const { show, toggle } = useToggle();

  return (
    <div>
      // 投入使用
      {show && <div>我是div</div>}
      <button onClick={toggle}>点击隐藏</button>
    </div>
  );
}

export default App;
```

# React Hook 的使用规则

- 截至目前, 我们已经认识了这些 Hook:
  - `useState`(状态),
  - `useRef`(获取 dom 元素),
  - `useContext`(底层组件实现于顶层通信),
  - `useEffect`(渲染自动调用, 根据依赖项配置再次调用),
  - `自定义hook`
- 它们使用都有以下规则
  - 只能在组件中或者其他自定义 Hook 中调用
  - 只能在组件顶层调用, 不能嵌套在 if , for 或者其他函数中
- 错误示范

```ts
import { useState } from "react";

// const [error ,setError] = useState(null);      // 错误! 不能在组件外调用

function App() {
  // if(true) {
  //   const [error, setError] = useState(null);  // 错误! 不能嵌套在 if, for 或其他函数中
  // }

  return (
    <div>
      <p>注释部分都是错误示范</p>
    </div>
  );
}

export default App;
```

> **就该定义在组件函数的最上面**

# Redux

> 类似于 `Vue.Pinia` => 集中状态管理工具

### Redux 实现数据修改的逻辑

1. `state`: 一个存放着需要被管理管理的对象
2. `action`: 一个用于描述如何修改对象的对象
3. `reducer`: 一个函数, 根据你提供的`action`, 生成一个新的`state`

### Redux 在 React 中使用

1. 安装两个插件: `npm i @reduxjs/toolkit react-redux`

   - `tooklit` => 简称 RTK, 封装了很多操作 redux 的工具包(简化代码)
   - `react-redux` => redux 和 react 组件通信的中间件(连接二者)
   - 打开`package.json`依赖声明部分, 确保已成功导入

2. 投入使用: 新建 `@/store/`, 并在该目录下新建
   - `modules/` => 存放状态管理文件
   - `index.ts` => 用于作为出口, 导出状态管理文件
   - 通常来说, 目录结构都是这么设计的

### 使用`Redux`实现`Count`案例

1. 新建 `@/store/counterStore.ts`

```ts
// 导入createSlice
import { createSlice } from "@reduxjs/toolkit";

// 定义 state 类型 (ts)
export interface CounterState {
  count: number;
}

const counterStore = createSlice({
  // 配置名称
  name: "counter",

  // 初始化状态
  initialState: {
    count: 0,
  },

  // 编写修改数据的方法
  reducers: {
    increment: (state) => {
      state.count += 1;
    },

    decrement: (state) => {
      state.count -= 1;
    },
  },
});

// 结构赋值获取actions
const { increment, decrement } = counterStore.actions;
// 获取reducer
const reducer = counterStore.reducer;

// 以按需导出的方式导出actions
export { increment, decrement };
// 以默认导出的方式导出reducer
export default reducer;
```

2. 编辑 `@/store/index.ts`

```ts
// 导入configureStore
import { configureStore } from "@reduxjs/toolkit";
// 导入子模块reducer
import counterReducer from "./modules/counterStore";

const store = configureStore({
  // 组合子模块reducer
  reducer: {
    counter: counterReducer,
  },
});

// 定义 RootState 类型
export type RootState = ReturnType<typeof store.getState>;

// 导出store
export default store;
```

3. `@/main.ts` 中注入`store`

```ts
// 导入核心依赖
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
// 导入store
import store from "./store";
// 导入Provider
import { Provider } from "react-redux";

// 导入根组件
import App from "./App.tsx";

// 渲染根组件到id为 <root> 的Dom元素上
createRoot(document.getElementById("root")!).render(
  <StrictMode>
    {/* 使用Provider包裹根组件, 实现注入store */}
    <Provider store={store}>
      <App />
    </Provider>
  </StrictMode>
);
```

4. 在 `@/App.tsx` 中投入使用

```tsx
// 导入useSelector, useDispatch
import { useSelector, useDispatch } from "react-redux";
// 导入RootState
import { RootState } from "./store";
// 导入actions
import { increment, decrement } from "./store/modules/counterStore";

function App() {
  // 获取状态
  const count = useSelector((state: RootState) => state.counter.count);

  // 获取dispatch
  const dispatch = useDispatch();

  return (
    <div>
      <button onClick={() => dispatch(decrement())}>-</button>
      <h1>{count}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
}

export default App;
```

- 针对`typescript`的难点:
  - 在 `counterStore.ts` 中: 以定义接口的形式, 添加了 `CounterState` 接口来定义计数器状态的类型
  - 在 `index.ts` 中: 添加了 `RootState` 类型, 它代表整个 store 的状态类型, 使用 `ReturnType<typeof store.getState>` 来自动推导出完整的状态类型(溯源可以找到`redux.d.ts`里的相关声明)
  - 在 `App.tsx` 中: 通过导入 `RootState`, 实现了将 `useSelector` 中的 **any 类型替换为具体的 RootState** 类型

### action 传递参数

1. 编辑`counterStore.ts`, 增加方法

```ts
    // ...
    // 编写修改数据的方法
    reducers:{
        increment: (state) => {
            state.count += 1;
        },

        decrement: (state) => {
            state.count -= 1;
        },

        // 增加方法, 第一参数不变还是state, 第二参数是action
        addToNum: (state, action) => {
            // 通过 action.payload 获取action传进来的参数
            state.count += action.payload;
        },

        minusToNum: (state, action) => {
            state.count -= action.payload;
        }
    }

    // ...

// 直接导出(新增 addToNum, minusToNum)
export const { increment, decrement, addToNum, minusToNum } = counterStore.actions;
export default counterStore.reducer;
```

2. 编辑 `App.tsx`:

```tsx
// 导入actions(新曾 addToNum, minusToNum)
import {
  increment,
  decrement,
  addToNum,
  minusToNum,
} from "./store/modules/counterStore";

// ...

return (
  <div>
    <button onClick={() => dispatch(decrement())}>-</button>
    // 投入使用时传参
    <button onClick={() => dispatch(minusToNum(10))}>-10</button>
    <h1>{count}</h1>
    <button onClick={() => dispatch(increment())}>+</button>
    <button onClick={() => dispatch(addToNum(10))}>+10</button>
  </div>
);
```

- 在`store`文件中, 定义新的`reducer`, 参数都是`(state, action)`
- 在获取组件传递过来的参数时, 是使用第二参数的 payload 属性: `action.payload`

### 异步获取数据

- 新建`@/store/channels.ts`

```ts
import { createSlice } from "@reduxjs/toolkit";
import axios from "axios";

// 定义 channel 类型
export interface Channel {
  userId: number;
  id: number;
  title: string;
  completed: boolean;
}

// 定义 state 类型
export interface ChannelState {
  // state 为 Channel 组成的列表
  channelList: Channel[];
}

const channelStore = createSlice({
  name: "channel",

  initialState: {
    channelList: [],
  },

  reducers: {
    setChannels(state, action) {
      state.channelList = action.payload;
    },
  },
});

// 异步获取数据
const { setChannels } = channelStore.actions;
const fetchChannelList = () => {
  return async (dispatch) => {
    const res = await axios.get("https://jsonplaceholder.typicode.com/todos");
    dispatch(setChannels(res.data));
  };
};

export { fetchChannelList };
export default channelStore.reducer;
```

2. 在`App.tsx`中导入并使用

```ts
// 导入 useEffect
import { useEffect } from "react";
// 导入useSelector, useDispatch
import { useSelector, useDispatch } from "react-redux";
// 导入actions
import { fetchChannelList } from "./store/modules/channelStore";

function App() {
  // 获取状态
  const channelList = useSelector(
    (state: RootState) => state.channel.channelList
  );
  // 获取dispatch
  const dispatch = useDispatch();
  // 使用useEffect, 声明在组件首次渲染时, 调用fetchChannelList(), 设置channelList的值
  useEffect(() => {
    dispatch(fetchChannelList());
  }, [dispatch]);

  return (
    <div>
      <ul>
        {channelList.map((channel) => (
          <li key={channel.id}>{channel.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

- 这一部分比较抽象, 只有记住格式, 想要异步获取数据

1. 正常写`createSlice`部分
2. 在本体获取`reducer`, 编写异步方法, 最后暴露`action`出去的是这个经过加工变成异步的方法: `fetchChannelList`
3. 在外部调用此`action`时, 用的是`useEffect`的形式调用