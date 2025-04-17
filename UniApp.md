# uni-app入门

## 简介

- 一款基于 Vue.js 的框架, 优势在于可以发布到多平台上(web, 小程序, 快应用, 安卓或ios原生应用等)

## 下载开发工具

- H Builder X <https://www.dcloud.io/hbuilderx.html>
- 这是 UniApp 官方提供的编辑器

## 新建项目

- 使用uniapp官方编辑器新建项默认目即可

## 目录结构

- `pages/` => **页面存放区域**
- `static/` => 静态文件存放区(图片, 字体, 音视频)
- `unpackage/` => 打包文件区(不用管)
- `App.vue` => 项目入口(Vue)
- `Main.js` => 主js(Vue)
- `manifest.json` => 项目配置文件
- `pages.json` => **页面配置文件**
- `uni.scss` => 预置scss文件

> 重点工作区域在 `pages.json` 和 `pages/` 中

## 运行项目

### 运行到浏览器

- 直接 `运行到Chrome` 即可

### 运行到微信小程序

- 下载微信开发者工具 <https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html>
- 配置 HBuilderX:
  - 设置微信开发者工具路径:[ctrl+,]开启设置, 运行配置, 配置微信开发者工具路径为: `/Applications/wechatwebdevtools.app` (Mac)
  - 获取AppID: 微信开发者工具, 新建项目, 获取“测试号”
  - 将测试号写入 `manifest.json` 中的微信小程序配置项的 AppID 中, 即可 `运行到微信开发者工具`

### 运行到安卓真机

- 先要在真机上打开开发者模式(问ai, 某型号某品牌的手机如何开启开发者模式)
- 准备一根支持 `usb调试` 的数据线
- 连接电脑后, HBuilderX, `运行到 Andriod App 基座` , 安装abd和插件后即可运行在手机上
- 手机上会开启一个HBuilder安装包, 安装完成后会自动打开, 就是我们开发的 App
- 需要注意有些脑残手机品牌, 会阻止你安装未在他们品牌自己的 AppStore 里上架的 app, 甚至还会推荐你去下载他们的, 只有想办法跳过

## pages.json

- `~/pages.json` 是页面配置文件, 分为单页配置和全局配置

```json
{
 // 单独页面配置
 "pages": [
  // 必须注册到pages才可以访问, 这里就是启动页
  { 
   // 页面路径
   "path": "pages/index/index",
   // 本页样式
   "style": {
    // 该页导航条文本, 当样式重名时, 优先使用本页样式覆盖全局样式
    "navigationBarTitleText": "学习 uni-app"
   }
  }
 ],
 
 // 全局样式
 "globalStyle": {
  // 文本颜色 back or white
  "navigationBarTextStyle": "white",
  // 导航条文本
  "navigationBarTitleText": "uni-app",
  // 导航条颜色 #rgb
  "navigationBarBackgroundColor": "#d2b48c",
  // 下拉背景颜色 #rgb
  "backgroundColor": "#d2691e",
  // 下拉刷新
  "enablePullDownRefresh": true
  // 还有更多配置项, 可以参考 https://uniapp.dcloud.net.cn/collocation/pages.html
 },
 "uniIdRouter": {}
}
```

- 所有页面必须以对象的形式写入 `pages[]` 数组才算注册成功, 才可以被访问到
- 页面可以有单独的 style, 当比如 `"navigationBarTitleText"` 即在全局样式中被定义, 又在某页自己的 style 中被定义, 那么单页配置优先级更高
- 更多配置项可以参考文档: <https://uniapp.dcloud.net.cn/collocation/pages.html>

### tabBar

- 什么是tabBar? 一个默认在底部的页面导航
- 先新建两个页面: `~/pages/mine/mine.vue`(个人信息), `~/pages/seckill/seckill.vue`(秒杀)
- 然后编辑 `pages.json`

```json
{
 "pages": [ 
  { 
   "path": "pages/index/index",
   "style": {
    "navigationBarTitleText": "学习 uni-app"
   }
  },
  // 1, 注册页面
  {
   "path": "pages/seckill/seckill"
  },
  {
   "path": "pages/mine/mine"
  }
 ],
 
 "globalStyle": {
  "navigationBarTextStyle": "black",
  "navigationBarTitleText": "uni-app",
  "navigationBarBackgroundColor": "#f0fff0"
 },
 "uniIdRouter": {},

 // 2, 配置 tabBar, 一个默认在底部的导航按钮组
 "tabBar": {
  // 文本颜色
  "color": "#3F536E",
  // 文本选中后的颜色
  "selectedColor": "1296db",
  // 边框颜色
  "borderStyle": "black",
  // 背景颜色
  "backgroundColor": "#f0fff0",
  // "position": "bottom", 默认是 bottom, 即在页面底部
  "list": [
   // 按序排列, 最少2个, 最多5个
   {
    "pagePath": "pages/index/index",
    // 选中前的图标
    "iconPath": "/static/home.png",
    // 选中后的图标
    "selectedIconPath": "/static/home_seleted.png",
    // 按钮名称
    "text": "首页"
   },
   {
    "pagePath": "pages/seckill/seckill",
    "iconPath": "/static/clock.png",
    "selectedIconPath": "/static/clock_selected.png",
    "text": "秒杀"
   },
   {
    "pagePath": "pages/mine/mine",
    "iconPath": "/static/user.png",
    "selectedIconPath": "/static/user_selected.png",
    "text": "个人信息"
   }
  ]
 }
}
```

- 需要先在 `pages[]` 里将页面注册, 再在 `tabBar.list[]` 里声明
- 图标可以从 iconfront 里下载 <https://www.iconfont.cn/collections/index>
