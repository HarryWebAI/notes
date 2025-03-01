# H-Blog 开发日志

### 准备视图模板.

- 面向 ai 编程,让 kimi 给我一个 bootstrap+JS 实现的个人博客 html
- 为了项目运行效率,把 html 中,css 和 js 指向的地址打开,复制代码到本地
- 重新定位 css 和 js 的资源地址

### 创建项目

- 使用 pycharm 创建
  - 新建项目
  - 选择环境时，不要选择虚拟环境，直接新建项目、自定义环境、选择 python 安装的目录
  - 不使用虚拟环境的原因是：
    - 我已经用`pip install django`以及`pip install mysqlclient`装了最新版本的 django 和 mysqlclient
    - 可以用`pip list`查看 python 全局包
    - 如果使用虚拟环境，在当前环境下装的包，不影响当前电脑的全局
- 不用`django-admin startproject HBlog`的原因是这个命令创建以后,不带/templates 文件夹,且需要自己去 settings.py 配置

### 创建 app home 主页并配置模板

- 执行命令`python manage.py startapp home`
- 编辑`./home/views.py`

```python
from django.shortcuts import render

# Create your views here.
def index(request):
    return render(request, 'index.html')
```

- pycharm 提示没有 index.html,导入我们写好的模板,发现黄色波浪线消失,确定导入成功
- 新建`./home/urls.py`,配置项目路由

```python
from django.urls import path
from . import views

app_name = 'home'

urlpatterns = [
    path('', views.index,),
]
```

- 配置总路由`./HBlog/.urls` 

```python
from django.urls import path,include

urlpatterns = [
    path('', include('home.urls')),
]
```

- 启动项目,发现没有 css 和 js,配置静态文件
- 新建`./static/`,以及下面的`css/`和`js/`文件夹
- 把准备好的 css 和 js 放进去
- 在主页模板`./templates/index.html`中,第一行声明加载静态文件

```html
{% load static %}
```

- 还有一种一劳永逸的方法:就是配置`./HBlog/settings.py`

```python
TEMPLATES = [
    {
            # 略
            'context_processors': [
                # 略
            ],
            # 就是这一句
            # 注意和'context_processors'平级,不要写到里面去了.项目里实际是注销了的.
            # 因为index.html作为唯一的父模板,只载入一次
            'builtins': ['django.templatetags.static']
        },
    },
]
```

- 再次强调:`'builtins': ['django.templatetags.static']`
  配置后可以一劳永逸,不用在每个需要记载静态文件夹的模板中`{% load static %}`
- 还得配置`./HBlog/settings.py`

```python
STATIC_URL = 'static/'
# 不要忘记这一句,告诉django,静态文件放在哪些目录中:
STATICFILES_DIRS = [
    BASE_DIR / 'static'
]
```

- 最后编辑模板,载入 css 和 js 文件

```html
<!-- 引入Bootstrap CSS -->
<link href="{% static 'css/bootstrap.min.css' %}" rel="stylesheet" />
<!-- 自定义样式 -->
<link href="{% static 'css/style.css' %}" rel="stylesheet" />
# 略
<!-- 引入Bootstrap和依赖的JS -->
<script src="{% static 'js/popper.min.js' %}"></script>
<script src="{% static 'js/bootstrap.min.js' %}"></script>
```

- 需要注意:`src=""`一定要双引号,里面`{% static '注意这里单引号声明路径' %}`
- css 引入的顺序:bootstrap,自己的 style
- js 引入的顺序:popper,bootstrap

### 创建 app post 文章并配置模板

- 执行命令,新建 app`python manage.py startapp post`
- 新建`/templates/post_list.html`

```html
{% extends 'index.html' %} {% block content %}
<div class="post-list">
  <h2>最新文章</h2>
  <hr />
  <div class="list-group">
    {% for post in posts %}
    <a
      href="{% url 'post_detail' post.id %}"
      class="list-group-item list-group-item-action"
    >
      <h3 class="mb-1">{{ post.title }}</h3>
      <p class="mb-1">{{ post.content|truncatewords:30 }}</p>
      <small class="text-muted"
        >发布日期：{{ post.publish_date|date:"Y-m-d" }}</small
      >
    </a>
    {% empty %}
    <p>暂无文章。</p>
    {% endfor %}
  </div>
</div>
{% endblock %}
```

- 重点是`{% extends 'index.html' %}` 继承 index.html,并且填充 index.html 中的占位符
- 在`./templates/index.html`中声明占位

```html
{% block content %}
<div class="col-md-8">
  <!--     子模板要填充这里面的内容     -->
</div>
{% endblock %}
```

- 编辑视图文件`.post/views.py`

```html
from django.shortcuts import render # 文章列表 def list(request): return
render(request,'post_list.html')
```

- 新建路由声明文件`./post/urls.py`

```html
from django.urls import path from . import views app_name = 'post' urlpatterns =
[ path('list', views.list,name='post_list'), # 文章列表 ]
```

- 编辑总路由`./HBlog/urls.py`

```html
from django.urls import path,include urlpatterns = [ path('',
include('home.urls')), # 主页 path('post/', include('post.urls')) # 文章 ]
```

- 在 index.html 中添加超链接

```html
<a class="navbar-brand" href="{% url 'home:home' %}">HBlog</a>
<a class="nav-link" href="{% url 'post:post_list' %}">文章</a>
```

- 一个是主页的,一个是文章列表页的
- 格式`{% url '模块名称:路由名称' %}`
- 模块名称和路由名称都声明在`./子app/urls.py`中,分别是`app_name`以及`path(...,name=这个参数)`

### 创建博客发布页面

- 在`./templates/`下创建`post_create.html`

```html
{% extends 'index.html' %} {% block content %}
<div>
  <h1>我是博客发布页面</h1>
</div>
{% endblock %}
```

- 在`./post/views.py`中写好视图函数:

```html
# 其余略 # 发布文章 def create(request): return
render(request,'post_create.html')
```

- 配置路由

```html
urlpatterns = [ path('list', views.list,name='post_list'), # 文章列表
path('create', views.create,name='post_create'), # 新增文章 ]
```

- 调整布局,侧边栏只有首页和文章列表页需要,发布页不需要,将侧边栏独立出来,创建`./templates/side_bar.html`
- 将 index 里,关于 side_bar 的代码剪切进去

```html
<!-- 侧边栏 -->
<div class="col-md-4">
  <div class="sidebar">
    <h3>关于我</h3>
    <br />
    <p>我是Harry,一位资深的,面向AI编程的web全栈工程师</p>
    <br />
    <h3>技术栈</h3>
    <br />
    <ul>
      <li><a href="#">html(html5+css+js)</a></li>
      <li><a href="#">Vue</a></li>
      <li><a href="#">Python</a></li>
      <li><a href="#">Django</a></li>
      <li><a href="#">面向AI编程 :)</a></li>
    </ul>
    <br />
  </div>
</div>
```

- 然后 index.html 和 post_list.html 里面:`{% include 'side_bar.html' %}`
- 导入<a href="https://www.wangeditor.com/">wangEditor</a>:

  - 为了加速,先把它的 css 和 js 存到项目里:`/static/css/`下`wangeditor.css`.JS 同理
  - index 父模板导入`<link rel="stylesheet" href="{% static 'css/wangeditor.css' %}">`.JS 同理
  - 这里需要注意:css 的顺序是,bootstrap->wangeditor->自己的 style
  - js 引用的顺序是,popper->bootstrap->wangeditor->自己的 js
  - 编辑`./templates/post_create.html`页面,其余省略,只展示与 wangEditor 有关的:

  ```html
  <div id="editor—wrapper">
    <div id="toolbar-container"><!-- 工具栏 --></div>
    <div id="editor-container"><!-- 编辑器 --></div>
  </div>
  ```

  ```css
  /* wangEditor */
  #editor—wrapper {
    border: 1px solid #ccc;
    z-index: 100; /* 按需定义 */
  }
  #toolbar-container {
    border-bottom: 1px solid #ccc;
  }
  #editor-container {
    height: 500px;
  }
  ```

  ```javascript
  /**
   * wangEditor
   */
  const { createEditor, createToolbar } = window.wangEditor;

  const editorConfig = {
    placeholder: "Type here...",
    onChange(editor) {
      const html = editor.getHtml();
      console.log("editor content", html);
      // 也可以同步到 <textarea>
    },
  };

  const editor = createEditor({
    selector: "#editor-container",
    html: "<p><br></p>",
    config: editorConfig,
    mode: "default", // or 'simple'
  });

  // 工具栏配置
  const toolbarConfig = {};

  const toolbar = createToolbar({
    editor,
    selector: "#toolbar-container",
    config: toolbarConfig,
    mode: "default", // or 'simple'
  });
  ```

- 最好在首页全部载入,但是会发现问题:
  - 首页,文章列表页,js 报错,说找不到一个 id 叫`editor-container`的元素
  - 这是因为我们只在 post_create.html 页面插入了这么一个元素,也就是专门用来存放 wangeditor 的容器
  ```javascript
  # 引用./static/js/post_create.js 内关于对wangeditor的配置
  const editor = createEditor({
    selector: '#editor-container', //就是这个元素找不到
    html: '<p><br></p>',
    config: editorConfig,
    mode: 'default', // or 'simple'
  })
  ```
  - 所以有强迫症的我们怎么能忍呢?把`index.html`导入编辑器的代码,搞到`post_create.html`里面:
  ```html
  {# 这是wangEditor #} {% load static %}
  <script src="{% static 'js/wangeditor.js' %}"></script>
  <script src="{% static 'js/post_create.js' %}"></script>
  ```
  - 又踩坑了,注意这一句`{% load static %}`,没写就报错,因为没配置`settings.py`里面的`TEMPLATES`属性
  - 属性里没加上这一句`'builtins': ['django.templatetags.static']`,所以每次载入静态文件,都要加上这句话
  - 最后,在`post_list.html`
    里,给发表文章按钮添加上 url`<a href="{% url 'post:post_create' %}" class="btn btn-dark">发表文章</a>`
  - 路由指向`post_create`,对应`post模块.views视图文件.create方法`,意为发表文章

### 登录和注册

- 新建模板`./templates/login.html`登录
- 新建模板`./templates/register.html`注册

```html
{% extends 'index.html' %} {% block content %}
<div class="text-center">
  <h1>登录</h1>
</div>
{% endblock %}
```

- 确认 django 自带 auth 这个 app

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth', #这就是django自己的用户app
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

- 但我们不用这个，我们用自己的，新建 app `blogauth`
- 新建 app 是为了防止冲突，执行命令`python manage.py startapp blogauth`
- 配置入口视图`./blogauth/views.py`

```python
from django.shortcuts import render

# 登录
def login(request):
    return render(request, 'login.html')

# 注册
def register(request):
    return render(request, 'register.html')
```

- 配置模块化路由

```python
# blogauth.urls
from django.urls import path
from . import views

app_name = 'blogauth'

urlpatterns = [
    path('login', views.login,name='login'), #登录
    path('register', views.register,name='register'), #注册
]

# HBlog.urls
from django.urls import path,include
urlpatterns = [
    path('', include('home.urls')), # 主页
    path('post/', include('post.urls')), # 文章
    path('blogauth/', include('blogauth.urls')) # 用户(新增)
]

```

- 访问一下，看得见了，在模板上增加注册和登录的表单，略
- 在主页模板上挂上 url：举例`href="{% url ('AppName:UrlName') %}"`
- 最后整理一下模板,给 title 加上占位,子模板继承,以便网页标题随着路由切换而修改.

### 实现验证码功能

- 选择 QQ 邮箱打开,设置,第三方服务,把 IMAP/SMTP 服务打开,验证后获取了一个授权码
- 配置给 django,编辑 ./HBlog/settings.py

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_USE_TLS = True
EMAIL_HOST = 'smtp.qq.com'
EMAIL_PORT = 587
EMAIL_HOST_USER = '你的邮箱'
EMAIL_HOST_PASSWORD = '第一部获取的授权码'
DEFAULT_FROM_EMAIL = '默认发送的邮箱地址,个人版的也是用你的邮箱'
```

> 码云上公开源码的时候上传的时候记得干掉

- 编辑`./blogauth/views.py`,增加一个发送邮箱验证码的函数

```python
.
.
.
# 导包
from django.http.response import JsonResponse # 导入django的json
import random #导入随机包
import string #导入字符包
from django.core.mail import send_mail # 导入django的邮件发送功能

# 发送验证码
def send_email_captcha(request):
    email = request.GET.get('email') # 通过 HTTP_GET 方式获取get 到 网页提交的email
    if not email : # 如果没有email
        return JsonResponse({'code': 400, "message": "邮箱不正确!"}) # 传回json,代码400,信息为邮箱不正确!
    # 生成验证码
    captcha = "".join(random.sample(string.digits, 4)) # 从string.digits里面随机挑选4个数组 (string.digits= [1,2,3,4,5,6,7,8,9,0]),最后"".join用空字符链接列表形成字符串
    send_mail("HBlog注册:请接受你的验证码",f"HBlog提醒您:\r\r当前注册的验证码是{captcha}",recipient_list=[email], from_email=None)
    return JsonResponse({'code': 200, "message": "验证码发送成功"})
```

- 现在我们只完成了这样的功能:访问 `localhost/blogauth/captcha?email=邮箱地址`,调取 send_email_captcha()
  函数,自动发送验证码到路由指定的邮箱中
- 所以我们还需要:一个存储当前验证码的容器,通常是通过 redis 缓存实现,但我们这里使用数据库

### 开始配置数据库

- 先在 mysql 中创建数据库,命令行登录 mysql 后:`create database hblog;`

> 结合上面的 smtp 服务,我害怕别人借我的 qq 邮箱进行非法活动,所以我需要确保我的邮箱和 smtp 服务授权码只被本项目使用,不能把 setting.py 的配置信息上传给码云
> 当然数据库的也不行

- 新建`/.gitignore`,即 git 忽略文件,告诉 git,不要追踪里面的内容

```
database.cnf
smtp.cnf
```

- 新建`database.cnf`和`smtp.cnf`用于保存二者信息,编辑:

```editorconfig
# database.cnf
[database]
DB_NAME = 数据库名称
DB_USER = 数据库用户
DB_PASSWORD = 数据库密码
DB_HOST = 数据库主机,默认localhost
DB_PORT = 数据库端口,默认3306

# smtp.cnf
[smtp]
SMTP_HOST = smtp服务器地址
SMTP_PORT = 默认端口587
SMTP_USER = 邮箱用户名
SMTP_DEFUALT_FROM_EMAIL = 默认发送邮件的账号
SMTP_PASSWORD = 申请的验证码
```

- 导入到 `HBlog/setting.py` 中

```python
# 最上方导包
import os
from configparser import ConfigParser

# 数据库配置项
database_config = ConfigParser()
database_config.read(os.path.join(BASE_DIR, 'database.cnf'))
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 或者你使用的数据库类型
        'NAME': database_config.get('database', 'DB_NAME'),
        'USER': database_config.get('database', 'DB_USER'),
        'PASSWORD': database_config.get('database', 'DB_PASSWORD'),
        'HOST': database_config.get('database', 'DB_HOST'),
        'PORT': database_config.get('database', 'DB_PORT'),
    }
}

# smtp服务配置项
'''
QQ smtp服务配置
'''
smtp_config = ConfigParser()
smtp_config.read(os.path.join(BASE_DIR, 'smtp.cnf'))
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_USE_TLS = True  # 如果你的SMTP服务需要TLS
EMAIL_HOST = smtp_config.get('smtp', 'SMTP_HOST')
EMAIL_PORT = smtp_config.getint('smtp', 'SMTP_PORT')  # 注意转换为整数
EMAIL_HOST_USER = smtp_config.get('smtp', 'SMTP_USER')
EMAIL_HOST_PASSWORD = smtp_config.get('smtp', 'SMTP_PASSWORD')
DEFAULT_FROM_EMAIL = smtp_config.get('smtp', 'SMTP_DEFUALT_FROM_EMAIL')
```

### 创建 captcha 模型

- 编辑 `./blogauth/models.py`

```python
# 验证码模型
class CaptchaModel(models.Model):
    email = models.EmailField(unique=True)
    captcha = models.CharField(max_length=4)
    create_time = models.DateTimeField(auto_now_add=True)
```

- 这里又有个坑，如果此时执行执行`python manage.py makemigrations`，会显示“No changes detected”
- 原来是我们忘记挂载 app 了，编辑 settings.py：

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # 挂载app
    'home',
    'post',
    'blogauth',
]
```

- 执行`python manage.py makemigrations` & `migrate` 命令，建表建库
- 编辑`/blogatu/views.py`文件，更新`send_email_captcha`方法：

```python
from .models import CaptchaModel #导入当前目录下models文件中的CaptchaModel

# 发送验证码
def send_email_captcha(request):
    email = request.GET.get('email') # 通过 HTTP_GET 方式获取get 到 网页提交的email
    if not email : # 如果没有email
        return JsonResponse({'code': 400, "message": "邮箱不正确!"}) # 传回json,代码400,信息为邮箱不正确!
    # 生成验证码
    captcha = "".join(random.sample(string.digits, 4)) # 从string.digits里面随机挑选4个数组 (string.digits= [1,2,3,4,5,6,7,8,9,0]),最后"".join用空字符链接列表形成字符串
    CaptchaModel.objects.update_or_create(email=email, defaults={'captcha': captcha}) # 依据email查找，如果没找到，创建一条新数据，找到了，更新email相等的那条数据的captcha字段，值为上面生成的随机数
    send_mail("HBlog注册:请接受你的验证码",f"HBlog提醒您:\r\r当前注册的验证码是{captcha}",recipient_list=[email], from_email=None)
    return JsonResponse({'code': 200, "message": "验证码发送成功"})
```

- 访问该路由地址：`localhost:8000/blogauth/captcha?email=随便输个邮箱地址`
- 再看数据库，已经有了一条数据，邮箱对应验证码，自带创建时间。
- 总结一下逻辑：

> 当用户点击发送验证码（这一步还没做，但我们可以通过直接访问路由带参数的形式实现访问），然后访问到 blogauth/views.py 中的 send_email_captcha()
> send_email_captcha 将自动生成一个 4 位数的验证码，将邮箱、验证码尝试写入 CaptchaModel，即数据库中的 blogauth_captchamoel 表中
> 用.objects.update_or_create()方法，查找第一个元素是否存在，即 email（模型中带唯一约束的字段）是否存在，存在则更新该条数据，不存在则新建一条数据
> 最后我们在数据库中就有了一个邮箱对应的验证码数据，在后期我们注册时，就可以通过这样的方法进行验证
> 但是这种方法是借用数据库，这并不好，以后还是要用 redis。

### Ajax 发送验证码

- 下载 jquery 至`./templates/js/`目录
- 导入 jquery，在`./templates/index.html`中：`<script src="{% static 'js/jquery.js' %}"></script>`
- 如何合理载入 js 文件？
  - 前面我们导入 wangeditor 就已经出现过了，我们只在文章发表页面载入 wangeditor 相关的 js
  - 我们可以用两种方法解决用户访问不同页面，加载所需 js 代码的方法：
  - 第一种就是在需要用到特定 js 的子模版中`{% load static %}`后再导入相关 js 代码
  - 第二种就是父模板`{% block head %}` 占位处导入页面所需的 js `{%end block%}`
- 我们还是用第一种方法，新建`register.js`

```javascript
/*
 * 注册页面:获取验证码
 * */
// 当页面加载后,执行一下函数
window.onload = function () {
  // 配置邮箱验证表达式
  let emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  function getMyCaptcha() {
    $("#get_captcha").click(function (event) {
      // 获取id叫做"get_captcha"的元素.给它绑定点击事件
      let $this = $(this); //把这个元素转为jquery对象
      let email = $("input[name='email']").val(); // 获取页面的email
      // 验证邮箱
      if (emailRegex.test(email)) {
        // 如果验证成功,即email的值匹配邮箱验证表达式
        // 如果邮箱验证通过了,先取消点击事件(意为只能点一次有效)
        $this.off("click");
        // 发送Ajax请求
        $.ajax("captcha?email=" + email, {
          meth: "GET",
          success: function (result) {
            if (result["code"] == 200) {
              alert("验证码发送成功");
            } else {
              alert(result["message"]);
            }
          },
          fail: function (error) {
            console.log(error);
          },
        });
        // 定义计时器
        let countdown = 60; // 倒计时60秒
        let timer = setInterval(function () {
          // setInterval(要执行的代码, 间隔多少秒执行一次,单位毫秒)
          if (countdown <= 0) {
            // 倒计时结束时
            $this.text("点击获取"); // 按钮文本变成'重新获取'
            clearInterval(timer); // 清除倒计时函数
            getMyCaptcha(); // 再次调用一次该函数
          } else {
            $this.text("重新获取:" + countdown + "s");
            countdown--; //coutdown自减
          }
        }, 1000); // [, 1000] 意为1秒执行一次
      } else {
        alert("邮箱格式不正确"); // 否则弹窗提醒,结束函数
        return null;
      }
    });
  }

  getMyCaptcha(); // 网页载入后,调用该函数
};
```

> 在这里吐槽一下,这鸟 js 确实不是人写的!
> 另外复制 js 代码时,耐心等网页转完,着急忙慌拷过来,居然提示我 $.ajax undefined,排查了好久,才发现,代码没复制完

- 最后在 register.html 里面载入

```html
{% load static %}
<script src="{% static 'js/register.js' %}"></script>
```

- 输入邮箱,点击验证一下,功能 ok,发送的验证码和数据库里的一样,ok!
- 关于$.ajax()

```javascript
$.ajax(第一个参数是url, {
  // url可以用字符串拼接
  // 第二个参数就是{}里面的,有这样几个主要配置项
  // 一是method,配置方法GET或者POST,
  // 二是请求成功后的回传success: 后面一般跟个匿名函数function(这是接收值){...},
  // 三是请求失败后的回传的fail: 同上,
  // 如果有需要,还可以complete: 同上,
  // 这个接收值,就是我们 blogauth/views.py 里面 send_email_captcha()函数写好的
  // return JsonResponse({这里面返回给前端ajax,是一个json})
});
```

### 注册功能实现

- 新建表单文件`./blogauth/forms.py`,用于验证用户输入的注册数据

```python
from django import forms
from django.contrib.auth import get_user_model # 这是django的官方User模型,对应auth_user表
from .models import CaptchaModel # 验证码模型

User = get_user_model()
# 定义注册表单
class RegisterForm(forms.Form):
    # 配置验证字段
    username = forms.CharField(
        max_length=20,
        min_length=2,
        error_messages={
            'required': '用户名不能为空',
            'max_length': '用户名不能超过20个字符',
            'min_length': '用户名过短,最少2位'
        }
    )
    email = forms.EmailField(
        error_messages={
            'required': '邮箱不能为空',
            'invalid': '邮箱格式不正确'
        }
    )
    captcha = forms.CharField(
        max_length=4,
        min_length=4,
        error_messages={
            'required': '验证码不能为空',
            'max_length': '验证码不正确',
            'min_length': '验证码不正确',
        }
    )
    password = forms.CharField(
        max_length=20,
        min_length=6,
        error_messages={
            'required': '密码不能为空',
            'max_length': '密码不能超过20个字符',
            'min_length': '密码过短,最少6位'
        }
    )

    # 验证邮箱是否唯一
    def clean_email(self):
        email = self.cleaned_data.get('email')
        exists = User.objects.filter(email=email).exists()
        if exists:
            raise forms.ValidationError('邮箱已经被注册!')
        return email

    # 验证邮箱和验证码是否匹配
    def clean_captcha(self):
        email = self.cleaned_data.get('email')
        captcha = self.cleaned_data.get('captcha')

        match = CaptchaModel.objects.filter(email=email, captcha=captcha).first()
        if not match:
            raise forms.ValidationError('验证码不正确!')
        match.delete()
        return captcha

    # 验证两次密码是否相同
    def clean_password(self):
        password = self.cleaned_data.get('password')
        password_check = self.cleaned_data.get('password_check')
        if not password_check == password:
            raise forms.ValidationError('两次密码输入不一致!')
        return password
```

- 注意`clean_字段(self)`方法:返回值是一个通过验证的同名字段,在里面写上验证逻辑,会自动完成验证
- 注意 `raise forms.ValidationError('错误信息')`方法:会直接生成一个 html 标签传回给 form 对象
- 编辑视图`./blogauth/views.py`,完善注册逻辑:

```python
.
.
.
from django.views.decorators.http import require_http_methods #导入装饰器
from .forms import RegisterForm #导入注册表单
from django.contrib.auth import get_user_model #导入官方的user模型

User = get_user_model() #全局实例化一个user模型
.
.
.
@require_http_methods(['GET','POST']) #装饰器限制:接受的http请求方法限制为GET和POST,也就是说只有GET和POST请求可以访问该方法
def register(request):
    if request.method == 'GET': # 如果是GET请求,说明是访问注册页面
        return render(request, 'register.html')
    else: # 要么GET,要么POST,是POST说明是提交注册表单
        # 验证表单数据
        form = RegisterForm(request.POST) #直接实例化RegisterForm表单类,把POST方法接收的参数传给该类,作为类属性
        if form.is_valid(): #form.is_valid()函数会匹配字段规则,如果所有写好的字段限制没有问题,那么会返回真
            # 处理相关字段
            email = form.cleaned_data.get('email')
            username = form.cleaned_data.get('username')
            password = form.cleaned_data.get('password')
            # 调用User新建账户
            User.objects.create_user(
                email = email,
                username = username,
                password = password
            )
            # 重定向到登录页面
            return redirect(reverse('blogauth:login'))
        else:
            # 如果表单数据验证失败,重新回到登录页面,并且把表单错误信息带回去,参数名字叫errors
            return render(request, 'register.html', context={'errors': form.errors})
```

- 现在,编辑模板 `./templates/register.html` ,配置`CSRF`并且展示错误信息

```html
<form ...>{% csrf_token %}</form>

{# 错误信息 #}
<div class="m-auto w-50" style="color:red">
  {% if errors %}
  <p>!错误:</p>
  {{ errors }} {% endif %}
</div>
```

> csrf_token 在 django 中,应该出现在每个模板文件的表单内!
> 如果加了还报错,记得刷新

- 配置`login.html`,给它也加上`csrf_token`
- 完善验证码发送功能:`./blogauth/views.py`,在发送验证码前验证一次邮箱

```python
def send_email_captcha(request):
    .
    .
    .
    # 邮箱已存在
    user = User.objects.get(email=email)
    if user:
        return JsonResponse({'code': 400, 'message': '邮箱已被注册'})
    .
    .
    .
```

### 登录功能实现

- 模板`./templates/login.html`加上错误提示信息

```html
<div class="m-auto w-50" style="color:red">
  {% if errors %}
  <p>!错误:</p>
  {{ errors }} {% endif %}
</div>
```

- 表单`./blogauth/forms.py`新增 LoginForm 登录表单:

```python
class LoginForm(forms.Form):
    email = forms.EmailField(
        error_messages={
            'required': '邮箱不能为空',
            'invalid': '邮箱格式不正确'
        }
    )
    password = forms.CharField(
        max_length=20,
        min_length=6,
        error_messages={
            'required': '密码不能为空',
            'max_length': '密码不能超过20个字符',
            'min_length': '密码过短,最少6位'
        }
    )
    # 记住我?
    remember = forms.IntegerField(required=False)
```

> 邮箱密码都可以复制
> 注意记住我字段是 IntergerField(可以为空)
> 踩了一个坑,pycharm 自动填充帮我把 Interger 写成 ImageField 了,怎么拿都是 None

- 编辑`/blogauth/views.py`,完成登录逻辑:

```python
.
.
.
from .forms import RegisterForm,LoginForm #把登录表单拿进来
from django.contrib.auth import login as user_login #这里导入login方法,与自己的login重名了,所以 as 取别名叫 user_login

# 登录
@require_http_methods(['GET','POST'])
def login(request):
    if request.method == 'GET':
        return render(request, 'login.html')
    else:
        form = LoginForm(request.POST)
        if form.is_valid():
            email = form.cleaned_data.get('email')
            password = form.cleaned_data.get('password')
            # 是否记住我?
            remember = form.cleaned_data.get('remember')
            # 获取用户
            user = User.objects.filter(email=email).first()
            # 用户存在,且密码正确
            if user and user.check_password(password):
                # 登录,创建session
                user_login(request, user)
                # 判断记住我,是否为1
                if not remember == 1:
                    request.session.set_expiry(0)
                return redirect('/')
            else:
                form.add_error('email', '邮箱或密码不正确')
                return render(request, 'login.html', context={'errors': form.errors})
        else:
            form.add_error('email', '邮箱或密码不正确')
            return render(request, 'login.html', context={'errors': form.errors})
```

> 这里有个大坑:就是`user = User.objects.filter(email=email).first()`这一句
> 我最先开始用的.get(email=email),如果邮箱不存在,会直接报错所以还是用 filter 筛选后.first()获取第一条数据
> 这个`user_login()`就是官方 User 模型的登录方法
> 吐槽:包真的好多...

- 登录成功后,重定向到首页,导航栏还是登录注册?

```html
{% if user.is_authenticated %}
<a
  class="nav-link dropdown-toggle"
  href="#"
  id="navbarDropdown"
  role="button"
  data-bs-toggle="dropdown"
  aria-expanded="false"
>
  {{ user.username }}
  <img
    src="{% static 'img/my_hero.jpg' %}"
    alt="{{ user.username }}"
    class="avatar"
    style="width:25px; height:25px; border:white; margin: 0;"
  />
</a>
<ul class="dropdown-menu dropdown-menu-end" aria-labelledby="navbarDropdown">
  <li>
    <a class="dropdown-item" href="{% url 'post:post_create' %}">发表文章</a>
  </li>
  <li><a class="dropdown-item" href="">退出</a></li>
</ul>
{% else %}
<a class="nav-link" href="{% url 'blogauth:login' %}" id="login-button">登录</a>
{% endif %}
```

- 通过`{% if user.is_authenticated %}`判断登录状态,登录了显示名字头像`{% else %}`显示登录按钮`{% endif %}`
- 登出,编辑`./blogauth/views.py`,添加 logout 函数:

```python
.
.
.
from django.contrib.auth import logout as user_logout
.
.
.
# 登出
def logout(request):
    # 退出登录,清除session
    user_logout(request)
    return render(request, 'index.html')
```

- 写路由,模板绑超链接,略

### 使用装饰器`@login_required()`装饰函数，实现访问某方法必须要登录的效果

- 以发表文章为例，要求登录后才可以发表文章，编辑`./post/views.py`中的 create 方法:

```python
from django.contrib.auth.decorators import login_required #导这个包获取login_required()装饰器

@login_required(login_url='/blogauth/login') #用装饰器login_required修饰create函数,要求发表文章前必须登录
def create(request):
    return render(request,'post_create.html')
```

- 关于 `login_required`,要求里面必须有一个参数`login_url`,指向指定路由,意为没登录就执行被装饰的函数时,自动跳转到指定路由
- 还有一种方法,在`./HBlog/settings.py`中,配置参数常量`LOGIN_URL`,这样装饰器就不用带参数了

```python
LOGIN_URL = '/blogauth/login'
```

### 博客发布功能

- 三个模型：`./post/models.py`

```python
from django.db import models
from django.contrib.auth import get_user_model

User = get_user_model()


# 分类
class Category(models.Model):
    name = models.CharField(max_length=16)


# 文章（博客）
class Blog(models.Model):
    title = models.CharField(max_length=32)
    content = models.TextField()
    public_time = models.DateTimeField(auto_now_add=True)
    category = models.ForeignKey('Category', on_delete=models.CASCADE)
    author = models.ForeignKey(User, on_delete=models.CASCADE)


# 评论
class Comment(models.Model):
    content = models.TextField()
    public_time = models.DateTimeField(auto_now_add=True)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
```

### admin 系统

- 配置 setting.py

```python
LANGUAGE_CODE = 'zh-hans' #中文,注意不是zh-CN

TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_TZ = False #把时区干掉
```

- 设置管理员权限,数据库中的用户改成员工和超级用户(is_staff=1, is_superuser=1)
- 配置`./post/admin.py`,使需要被管理的模型(数据表)挂载到 admin 页面中:

```python
from django.contrib import admin
from .models import Category, Blog, Comment


# Register your models here.
# 定义分类管理
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name']  # 展示name字段


# 定义博客管理
class BlogAdmin(admin.ModelAdmin):
    list_display = ['category', 'title', 'public_time', 'author']


# 定义评论管理
class CommentAdmin(admin.ModelAdmin):
    list_display = ['content', 'public_time', 'blog', 'author']


# 注册这三个模型管理模块
admin.site.register(Category, CategoryAdmin)
admin.site.register(Blog, BlogAdmin)
admin.site.register(Comment, CommentAdmin)
```

> 这里出问题了,挂载上了评论区点进去报错,说没有 blog.id 这一列
> 原来是我先开始没给 comment 加 blog 的外键,后面加上了,但是没有更新和执行迁移文件
> 在执行迁移文件时,又出现问题
> It is impossible to add a non-nullable field 'blog' to comment without specifying a default. This is because the
> database needs something to populate existing rows.
> Please select a fix:
>
> 1. Provide a one-off default now (will be set on all existing rows with a null value for this column)
> 2. Quit and manually define a default value in models.py.
>    这是说不能在评论表中新增字段 blog,要么选 1 它帮你自动设置为空,要么选 2 你自己取修改模型文件

- 编辑模型文件,"汉化模型":

```python
# 以Comment为例
class Comment(models.Model):
    content = models.TextField(verbose_name='评论内容')
    public_time = models.DateTimeField(auto_now_add=True, verbose_name='发布时间')
    blog = models.ForeignKey('Blog', on_delete=models.CASCADE, null=True, verbose_name='所属博客')
    author = models.ForeignKey(User, on_delete=models.CASCADE, null=True, verbose_name='评论人员')

    class Meta:
        verbose_name = '评论'
        verbose_name_plural = '评论'
```

- 通过`verbose_name = '字段汉化名称'`指定字段中文名称,以及`Class Meta:`定义 Meta 类,来汉化模型名称,`_plural`
  是复数的意思,中文没有复数所以都是'评论'
- 这样一来,访问`localhost:8000/admin`,用既是 staff,又是 superuser 的账户,就可以看见中文的,django 自带的后台管理页面了
- 添加几条 category

### 博客发布功能实现

- 编辑`./post/views.py`,完善 GET 请求后的渲染:读取 category,返回给视图模板

```python
.
.
.
from django.views.decorators.http import require_http_methods  # 限制访问方式


@require_http_methods(['GET', 'POST'])  # 只有post和get请求可以访问本方法
@login_required()  # 用装饰器login_required修饰create函数,要求发表文章前必须登录
def create(request):
    if request.method == 'GET':  # 如果是GET请求
        categories = Category.objects.all()  # 读取所有分类表
        return render(request, 'post_create.html', context={'categories': categories})  # 将分类数据传输给模板

.
.
.
```

- 在模板完成渲染:`./templates/post_create.html`:

```html
<div class="mb-3">
  <label for="" class="form-label">标签</label>
  <select name="category" class="form-select">
    {% for category in categories %}
    <option value="{{ category.id }}">{{ category.name }}</option>
    {% endfor %}
  </select>
</div>
```

> 注意是`{% for %}` ,一开始写成 `{{ for }}`,报错了

- 新建`./post/forms.py`,配置表单验证规则

```python
from django import forms

class CreateForm(forms.Form):
    title = forms.CharField(max_length=32,min_length=2,error_messages={
        'max_length': '标题过长,最多32位',
        'min_length': '标题不能少于2位'
    })
    category = forms.IntegerField()
```

- 完善 create():

```python
# 发布文章
@require_http_methods(['GET', 'POST'])  # 只有post和get请求可以访问本方法
@login_required()  # 用装饰器login_required修饰create函数,要求发表文章前必须登录
def create(request):
    if request.method == 'GET':  # 如果是GET请求
        categories = Category.objects.all()  # 读取所有分类表
        return render(request, 'post_create.html', context={'categories': categories})  # 将分类数据传输给模板
    else:
        form = CreateForm(request.POST)
        if form.is_valid():
            title = form.cleaned_data.get('title')
            category = form.cleaned_data.get('category'),
            content = request.POST['content']
            blog = Blog.objects.create(title=title, category_id=category[0], content=content, author=request.user)
            return JsonResponse({
                'code': 200,
                'message': '博客发布成功!',
                'blogId': blog.id
            })
        else:
            return JsonResponse({
                'code': 400,
                'message': '参数错误!'
            })
```

> 注意:`Blog.objects.create()`里面不能直接写`(category=category)`,因为我们从前端接受到的只有一个 id,不是一个完整的 category 对象
> 但是`author`相反,我们是直接从`request.user`里面获取的当前登录用户这个对象,直接传进`author`列即可
> 最后,就是 wangEdiotr,需要传 jason 回去.同时 wangEditor 还没有完善,只有两个 div 在模板上,根本不知道 content 是什么
> 莫名其妙的,category 变成了一个数组?
> 最后发现问题,原来我在接收`category = form.cleaned_data.get('category'),`时,多写了一个逗号,导致接受的不是单个值,而是一个元组,传给前端又被 json 转成了数组

- 完善前端:`./static/js/post_create.js`

```javascript
/*
 * wangEditor发送Ajax请求,提交内容
 * */
$("#submit-btn").click(function (event) {
  //找到id为submit-btn的按钮
  event.preventDefault(); //禁止它的默认事件

  let title = $("input[name='title']").val(); //获取文章名称数据
  let category = $("#category_select").val(); //获取分类数据
  let content = editor.getHtml(); //获取wangeditor数据
  let csrfmiddlewaretoken = $("input[name='csrfmiddlewaretoken']").val(); //获取csrf_token数据,名字不变方便下面ajax传参

  $.ajax("/post/create", {
    //ajax请求访问路由/post/create,即指向 post/views/create()
    method: "POST", //方法是POST
    data: { title, category, content, csrfmiddlewaretoken }, //同名参数传参,不用key:value
    success: function (result) {
      //请求成功
      if (result["code"] == 200) {
        //判断json返回的code
        window.location = "/post/detail/" + result.blogId; //是200则成功,跳转到 /post/detail/指定id
      } else {
        alert(result["message"]); //否则打印错误提示信息.
      }
    },
  });
});
```

> 总体来说,原生 js 屎一坨,即使有 jquery 还是屎

- 完善文章详情页面,`window.location = "/post/detail/" + result.blogId;`意为跳转到博客详情页面.
- 所以我们要编辑`./post/views.py`,新增`detail()`方法

```python
# 文章详情
def detail(request, id):
    try:
        blog = Blog.objects.get(pk=id)
    except Exception as e:
        blog = None
    return render(request, 'post_detail.html', context={'blog': blog})
```

- 然后我们还要整个模板:

```html
{% extends 'index.html' %} {% block title %}HBlog-文章{% endblock %} {% block
content %} {% if blog %}
<div class="col-md-8">
  <div class="text-center"><h1>{{ blog.title }}</h1></div>
  <hr />
  <div class="mt-2">{{ blog.content|safe}}</div>
  <hr />
  <div class="text-end">
    <p>
      {{ blog.public_time|date:'Y年m月d日' }},{{ blog.author }} | 分类:<a
        href="#"
        >{{ blog.category.name }}</a
      >
    </p>
  </div>
</div>
{% else %}
<div class="col-md-8">
  <p>警告:</p>
  <p>没有内容!</p>
</div>
{% endif %} {% include 'side_bar.html' %} {% endblock %}
```

- 学会使用过滤器:`{{ 值|调用过滤器 }}`,比如`{{ blog.content|safe}}`,意为内容安全,转成 html 标签
- `{{ blog.public_time|date:'Y年m月d日' }}`意为将时间转成年月日

### 评论功能

- 定义 comment 表单:`./post/forms.py`

```python
# 评论
class CommentForm(forms.Form):
    content = forms.CharField(min_length=2,max_length=100,error_messages={
        'max_length': '评论过长!最多100字!',
        'min_length': '评论过短!最少2个字!'
    })
    blog_id = forms.IntegerField()
```

- 定义`./post/views.py`下的 comment 函数

```python
# 评论功能
@require_http_methods(['POST'])  # 要求只有POST请求可以访问该函数
@login_required()  # 要求必须登录才能访问该函数
def comment(request):
    form = CommentForm(request.POST)  # 首先取得表单对象
    if form.is_valid():  # 验证表单
        # 清理数据
        content = form.cleaned_data.get('content')
        blog_id = form.cleaned_data.get('blog_id')
        # 写入到数据表
        Comment.objects.create(blog_id=blog_id, content=content, author=request.user)
        # 根据id返回文章
        return redirect('post:post_detail', id=blog_id)
    else:  # 如果验证没有通过
        message = form.errors  # 配置提示信息
        return render(request, 'message.html', {  # 到错误提示页面
            'message': message,  # 错误内容
            'referer': request.META.get('HTTP_REFERER', '/')  # 返回之前路由的链接
        })
```

-

配置视图,增加评论表单,只有一个点需要注意,为了传递 blog_id,可以使用`<input type="hidden" name="blog_id" value="{{ blog.id }}">`
隐藏的 input 标签

- 配置路由`./post/urls.py`
- 配置视图,`./templates/message.html`,作为错误提示页面

```html
{% extends 'index.html' %} {% block title %}HBlog-文章{% endblock %} {% block
content %}
<div class="col-md-8">
  <p>{{ message }}</p>
  <a href="{{ referer }}">返回</a>
</div>
{% include 'side_bar.html' %} {% endblock %}
```

- `<a href="{{ referer }}">返回</a>`,这里的 referer 就是视图传递过来的之前页面的路由

### 排序和分页

- 在访问文章详情时,需要评论逆序展示以显示最新的评论,同时为了页面美观,需要分页,编辑`./post/views.py`中的`detail()`方法:

```python
from django.core.paginator import Paginator  # 导入分页功能
.
.
.

def detail(request, id):
    try:
        # 根据主键获取博客
        blog = Blog.objects.get(pk=id)
        # 获取评论
        comments = Comment.objects.filter(blog_id=id).order_by("-id").all()  # 根据blog_id获取获取所有评论,根据id倒序排序
        page_size = 5  #设置每页5条评论
        paginator = Paginator(comments, page_size)  #开始分页
        page_number = request.GET.get("page", 1)  #当前页,默认为1
        comments = paginator.page(page_number)  #得到当前分页的数据,重新赋值给comments
    except Exception as e:
        blog = None
        comments = None
    return render(request, 'post_detail.html', context={'blog': blog, 'comments': comments})
```

- 同时在视图模板`./templates/post_detail.html`中

```html
<!-- 分页导航 -->
<div class="text-center">
  {% if comments.has_previous %}
  <a href="?page={{ comments.previous_page_number }}">上一页</a>
  {% endif %}

  <span
    >当前第 {{ comments.number }} of {{ comments.paginator.num_pages }}页</span
  >

  {% if comments.has_next %}
  <a href="?page={{ comments.next_page_number }}">下一页</a>
  {% endif %}
</div>
```

- 还有一种小技巧,可以通过 blog 直接获取 blog 下所有的评论

  - 第一步需要在./post/models.py 中声明 related_name

  ```python
  # 评论
  class Comment(models.Model):
    content = models.TextField(verbose_name='评论内容')
    public_time = models.DateTimeField(auto_now_add=True,verbose_name='发布时间')
    blog = models.ForeignKey('Blog', on_delete=models.CASCADE,related_name='comments', null=True,verbose_name='所属博客') #这一句
    author = models.ForeignKey(User, on_delete=models.CASCADE,null=True,verbose_name='评论人员')

    class Meta:
        verbose_name = '评论'
        verbose_name_plural = '评论'
  ```

  - 现在就可以通过`blog.comments`访问外键 blog 等于当前 blog_id 的所有数据了
  - 包括模板上,通过`{{ blog.comments.all }}`即可获得
  - 等同于`comments = Comment.objects.filter(blog_id=id).all()`只是没排序

### 首页,文章列表页

- 完善`./post/models.py`中的 Blog 模型,新增一个方法,去掉 html 标签,获取简略内容

```python
# 导这两个包
import re
from django.utils.text import Truncator


# 在Blog模型内
# 获取简略内容
def get_summary(self):
    """获取去除HTML标签的简略内容，显示在两行以内"""
    # 去除HTML标签
    text = re.sub(r'<.*?>', '', self.content)
    # 截取简略内容，假设每行50个字符，两行共100个字符
    return Truncator(text).chars(100, truncate='...')
```

- 在首页视图`./home/views.py`,完善首页方法`index`:

```python
# 首页
def index(request):
    # 获取最新2条博客的数据
    blogs = Blog.objects.all().order_by('-id')[:2]
    # 返回给首页模板
    return render(request, 'index.html', context={
        'blogs': blogs,
    })
```

- 在模板`./templates/index.html`中,循环数据展示

```html
{% if blogs %} {% for blog in blogs %}
<div class="post">
  <h3><a href="post/detail/{{ blog.id }}">{{ blog.title }}</a></h3>
  <p>{{ blog.get_summary }}</p>
  <small>{{ blog.public_time|date:"Y年m月d日 H:i" }}</small>
</div>
{% endfor %} {% endif %}
```

- 直接使用`blog.get_summary`获取简略内容
- 文章列表模板同理,`./templates/post_list.html`中,`blog.get_summary`直接拿到富文本不带 html 标签的简略内容
- 最后，搞定分页，略

### 使用`highlight.js`实现代码高亮

- <a href="https://highlightjs.org/download">HighLight 官方下载地址</a>
  - 可以定制需要的语言
  - 下载完成后是一个压缩包，我们需要里面的`./style/`CSS 主题，以及一个.min.js 文件
  - 下载需要的主题，将主题和 js 放在项目的静态文件夹中`./static/css & js`
- 在`index.html`加载主题,在`post_detail.html`加载 js 以及一句话:

```html
{% load static %}
<script src="{% static 'js/highlight.min.js' %}"></script>
<script>
  hljs.highlightAll();
</script>
```

### 搜索功能实现

- 先填之前的坑:之前无论评论还是文章列表,都需要手动倒叙排序`order_by('-id')`,但其实我们可以在模型中直接指定所有结果一劳永逸都是倒序

```python
# 某个某型的 Class Meta 里:
# ordering = ['-public_time'] # 默认排序方式:以发布时间倒序排序
```

- 编辑`./post/views.py`新增 search 方法:

```python
from django.db.models import Q  # 导入Q表达式
.
.
.

# 文章搜索功能
@require_http_methods(['GET'])  # 仅支持GET请求
def search(request):
    # 获取请求的参数
    keyword = request.GET.get('keyword')
    # 从请求的 GET 参数中获取当前页码，默认为第一页
    page = request.GET.get('page', 1)
    # 让模型找到数据 Q表达式,寻找标题带关键字的,或者内容带关键字的
    blog_list = Blog.objects.filter(Q(title__icontains=keyword) | Q(content__icontains=keyword)).order_by('-id').all()
    # 创建 Paginator 对象，每页显示 3 条数据
    paginator = Paginator(blog_list, 3)
    try:
        # 获取当前页的数据
        blogs = paginator.page(page)
    except PageNotAnInteger:
        # 如果页码不是整数，返回第一页数据
        blogs = paginator.page(1)
    except EmptyPage:
        # 如果页码超出范围，返回最后一页数据
        blogs = paginator.page(paginator.num_pages)
    # 返回视图,直接用文章列表那个模板
    return render(request, 'post_search_result.html', context={'keyword': keyword, 'blogs': blogs})
```

- 配置路由,略
- 模板上绑定，就是一个`<form action=设置好的url method='GET'>...</form>`即可
- 当然还有模板`sidebar.html`那些超链接也绑上,直接写超链接`<a href="/post/search?keyword=Python">Python</a>`

# 项目总结

### 目录结构

- 这是一个 demo 级的博客小项目，带我认识了 django 框架，它的目录结构通常是这样
  - 首先它有一个和项目文件夹同名的文件夹，比如我的项目叫`HBlog`,他里面还有一个`HBlog/`作为主 APP.
    - 主 APP 里面这个项目只用到了两个文件,`settings.py`以及`urls.py`,他们主要用于配置整个项目的环境和总路由管理
  - 其次,如果使用 pycharm 创建 django 项目,会自带一个`templates/`文件夹,用于存放 html 模板
  - 然后,我们的静态文件(CSS,JS,图片等),规范的话应该放在`static/`文件夹中
  - 最后,我们自己创建的 app,每个独立一个文件夹
  - 除了上面的之外,还有一个`manage.py`用于支持我们执行`cmd> python manage.py [相关命令]`

### 常用命令

- 创建 app`python manage.py startapp [应用名称]`
- 创建迁移文件`python manage.py makemigrations`

> 注意在创建迁移文件前,必须要先在 settings.py 中挂载应用

- 执行迁移命令`python manage.py migrate`,说白了也就是**建表改表**
- 运行项目`python manage.py runserver [端口不写默认8000]`

> 停止运行【ctrl+c】

### 通常一个项目，我们在 settings 里需要做：

- 配置时区和汉化

```python
# 简体中文
LANGUAGE_CODE = 'zh-hans'
# 时区
TIME_ZONE = 'Asia/Shanghai'
# 国际化?
USE_I18N = True
# 禁止时区感知
USE_TZ = False
```

> 第 1 项就是汉化功能,最直观的体现就在 django 自带的 admin 模块中
> 第 2 项时区也要记得更改
> 第 3,4 项目更多是开发跨境网站使用,如果是国内的小项目,应该都设置位 False

- 配置模板

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates', #模板引擎
        'DIRS': [BASE_DIR / 'templates'] #模板路径
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
            # 'builtins': ['django.templatetags.static'] #是否不需要在所有模板都声明{% load static %}后才能加载静态文件?
        },
    },
]
```

> 注意带注释的部分
> 如果通过 pycharm 创建项目,这些选项通常是自己写好了的,除了`builtins`

- 配置静态文件路径

```python
STATIC_URL = 'static/'
# 不要忘记这一句,告诉django,静态文件放在哪些目录中:
STATICFILES_DIRS = [
    BASE_DIR / 'static'
]
```

> 本项目经测试,通过 pycharm 新建 5.1.5 的 djaongo 框架项目,也不自带`STATICFILES_DIRS`这一项,需要自己手动写

- 配置数据库信息`DATABASES`,和下面一起讲

  - 这个项目还用到了配置 smtp 服务,但无论 smtp 和数据库,他们的信息都应该是保密的,为了 git 托管后信息安全,我应该:

    - 新建两个配置文件:`database.cnf`配置数据库,`smtp.cnf`配置 smtp 服务
    - 新建`.gitignore`,编辑其内容声明让 git 不追踪上面两个文件
      ```editorconfig
      database.cnf
      smtp.cnf
      ```
    - 然后再在 setting.py 中完成针对数据库和 smtp 服务的设置

      ```python
      # 首先导入两个包
      import os
      from configparser import ConfigParser

      # 配置数据库
      database_config = ConfigParser()
      database_config.read(os.path.join(BASE_DIR, 'database.cnf'))
      DATABASES = {
          'default': {
              'ENGINE': 'django.db.backends.mysql',  # 或者你使用的数据库类型
              'NAME': database_config.get('database', 'DB_NAME'),
              'USER': database_config.get('database', 'DB_USER'),
              'PASSWORD': database_config.get('database', 'DB_PASSWORD'),
              'HOST': database_config.get('database', 'DB_HOST'),
              'PORT': database_config.get('database', 'DB_PORT'),
          }
      }

      # 配置smtp服务
        smtp_config = ConfigParser()
        smtp_config.read(os.path.join(BASE_DIR, 'smtp.cnf'))
        EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
        EMAIL_USE_TLS = True  # 如果你的SMTP服务需要TLS
        EMAIL_HOST = smtp_config.get('smtp', 'SMTP_HOST')
        EMAIL_PORT = smtp_config.getint('smtp', 'SMTP_PORT')  # 注意转换为整数
        EMAIL_HOST_USER = smtp_config.get('smtp', 'SMTP_USER')
        EMAIL_HOST_PASSWORD = smtp_config.get('smtp', 'SMTP_PASSWORD')
        DEFAULT_FROM_EMAIL = smtp_config.get('smtp', 'SMTP_DEFUALT_FROM_EMAIL')
      ```

    - 配置默认登录路由(这个通常在我们完成登录功能后用)
      ```python
      # 配置登录路由
      LOGIN_URL = '/blogauth/login'
      ```

### 关于路由

- 路由模块化,就是主 APP 下的`urls.py`不直接写路由,而是`模块名/`

```python
from django.urls import path,include #导入include()
urlpatterns = [
    ...
     path('post/', include('post.urls')), # include('模块.urls'),
    ...
]
```

- 然后在子 APP 下的 urls 中,写入准确路由

```python
from django.urls import path
from . import views #导入视图

app_name = 'post' #配置名称

urlpatterns = [
    ...
    path('detail/<int:id>', views.detail,name='post_detail'), # 相关路由
    ...
]
```

> 一个正确的路由应该是`path('路由/<如果带参数>', views.指向的视图函数,name='路由别名'),`
> 访问该路由就应该是主 urls 里的`模块名/子路由/如果直接用斜杠传递GET参数`

- 路由传参有两种方式:一种是`url/指定参数`,一种是`url?参数名=参数值`
  - 第一种接受参数需要在视图函数定义时,直接接受`def view_example(request, 参数名)`
  - 第二种接受参数直接在视图函数中`变量名 = requset.GET.get('参数名')`

### 关于视图

> 首先我觉得本项目不够规范,好像绝大部分逻辑直接在视图层完成了:(,但毕竟是个 demo,暂时忽略吧

- 视图最重要的就是最后一定要`return`一个东西回去,要么是
  - `render(request, '指定模板', context={'要传递给模板的参数':参数的值, '还可以传递多个参数': 参数值2, ...})`
  - `redirect('app_name:url_name', 参数=值)`
- 视图排错通常需要 `from django.http import HttpResponse`,然后就可以`return HttpResponse(某个变量)`直接让视图函数返回一个指定变量了
- 用到 jsaon 时候,需要`from django.http.response import JsonResponse`,然后可以这样

```python
return JsonResponse({
    'code': 200, # 配置code
    'message': '博客发布成功!', # 配置message
    'blogId': blog.id # 配置参数
})
```

> 这样前端就会接受到这样一个 Json 字符串:`{'code': 200, 'message': ...}`就和我们配置的一样,然后在前端通过 ajax 的 success 方法判断 code 的值,实现 HTTP 异步请求交互

- 还有一个获得请求前页面的方法:`request.META.get('HTTP_REFERER', '/')`,获取请求前的 url,如果没有,就返回`/`,这个我们用在了`message.html`里面
- 最后有两个装饰器,一个限制访问请求类型,一个限制必须登录

```python
# 导包
from django.contrib.auth.decorators import login_required #限制登录访问
from django.views.decorators.http import require_http_methods #限制访问方式

# 实际使用
@require_http_methods['GET', 'POST'] #意为装饰器下面的函数,必须通过http.GET或者POST才能访问
@login_required() #意为装饰器下面的函数,必须登录才能访问
def example(request): #好了,example函数既需要GET/POST请求访问,又需要登录才能访问
    pass
```

- 最后一个小技巧就是通过`@require_http_methods['GET', 'POST']`修饰的函数,在函数内部判断`request.method`的值,来判断是提交表单,还是访问表单填写页面

### 关于 DTL 模板

- {% block %}
  - 父模板用 block & endblock 占位
  - 子模板用同样的标签填充占位区域
  - 这个项目里，我没有使用 block head/foot 进行占位，实际上，可以用 head / foot 加载特定页面所需的 css 和 js
- {% include %}
  - 某个 html 文件相当于组件，在某个需要该“组件”的模板中把它给 include 进来即可
- {% extend %}
  - 子模板继承
- {% if %}
  - 通常是用来判断有没有值从视图传递给模板，如果有某个值或者符合某个表达式，那么显示里面的内容，要么 else，记得 endif
- {% for %}
  - 同样是用来判断某个值，可以直接 empty 表示如果没有数据传递过来，那么显示指定内容，同样要 endfor
- {{ key }} 显示值

### 关于模型

- 模型就是数据表，定义一个模型通常有：

```python
from django.db import models #模型包
from django.contrib.auth import get_user_model #用户包
import re #用于替换字符串的包
from django.utils.text import Truncator #用于截断填充字符串的包

User = get_user_model() #获取全局变量User类

class Blog(models.Model):
    # 字段名 = models.字段类型(字段规则, verbose_name="字段中文名")
    title = models.CharField(max_length=32,verbose_name="标题")
    content = models.TextField(verbose_name="内容")
    public_time = models.DateTimeField(auto_now_add=True,verbose_name="发布时间")
    category = models.ForeignKey('Category', on_delete=models.CASCADE,verbose_name="所属分类")
    author = models.ForeignKey(User, on_delete=models.CASCADE,verbose_name="作者")

    class Meta:
        # “数据表”中文名
        verbose_name = '博客'
        # 复数的中文名
        verbose_name_plural = '博客'

    # 获取简略内容
    def get_summary(self):
        """获取去除HTML标签的简略内容，显示在两行以内"""
        # 去除HTML标签
        text = re.sub(r'<.*?>', '', self.content)
        # 截取简略内容，假设每行50个字符，两行共100个字符
        return Truncator(text).chars(100, truncate='...')
```

> 使用`User = get_user_model()`获取 django 自带的 User 用户类
> 模型类 => 实例化为对象 => 通过`对象.属性`或者`对象.方法`直接获取对应的值

- 外键
- 外键一定要搞清楚是一对一，一对多，多对多的关系。
  - 在配置外键字段时，如果是一对一，可以配置 `related_name=单数`。要获取关联的数据，直接`obj.单数单词`即可
  - 如果是一对多，可以`related_name=复数`，要获取对应的 query dict，直接`obj.复数单词`即可

### 关于表单

- 表单主要用于验证用户提交的数据

```python
from django import forms

# 发表文章
class CreateForm(forms.Form):
    title = forms.CharField(max_length=32,min_length=2,error_messages={
        'max_length': '标题过长,最多32位',
        'min_length': '标题不能少于2位'
    })
    category = forms.IntegerField()
```

- 定义该表单后,本项目在 views.py 中,是这么处理的

```python
# 先导包
from .forms import CreateForm,CommentForm
.
.
.
# 然后在某个视图函数中:
form = CreateForm(request.POST) # 直接把post数据传给表单类
if form.is_valid(): # 通过.is_valid()函数判断是否通过数据验证
    # 插入新的数据...
    title = form.cleaned_data.get('title')
    category = form.cleaned_data.get('category')
    content = request.POST['content']
    blog = Blog.objects.create(title=title, category_id=category, content=content, author=request.user)
    return JsonResponse({
        'code': 200,
        'message': '博客发布成功!',
        'blogId': blog.id
    })
.
.
.
```

> 主要就是通过`form.is_valid()`函数的返回值判断表单提交的数据是否合规
> 如果不合规,可以通过`form.errors`查看到不合规的数据(是 html 标签形式返回的)

### 自带的 auth 用户管理系统

- 在我们 settings.py 中,默认挂载了这些 app,其中第二个,就是自带的用户管理系统

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles'
]
```

- 所以任何时候,只要我们没有注销该 app,执行`python manage.py migrate`时,都会自动建 auth 模块相关的数据表
- 前面已经说过,我们可以在任何文件中,通过`from django.contrib.auth import get_user_model`获取 user 模型,然后`User = get_user_model()`交给 User 这个变量
- 注册:使用`User.objects.create_user(相关字段=值)`
- 登录:先通过`if user and user.check_password(password):`判断用户名密码是否正确后,`user_login(request, user)`即可

### 自带的 admin 后台管理系统

- 在主 app 的路由文件(./项目同名文件夹/usls.py)中,给我们定义了这样一条路由:`path('admin/', admin.site.urls),`
- 也就是说,只要我们通过`站点/admin`即可访问后台管理系统
- 但要访问后台管理系统,要求必须登录,且登录的用户既是`staff`又是`superuser`,我们这个项目是直接在数据库里面改的(两个字段 0 变 1)
- 但是 admin 默认只能管理用户,要想它管理其他模型(其他数据表),需要

```python
# 导admin包,导模型
from django.contrib import admin
from .models import Category, Blog, Comment
# 分别定义要管理的表和每个表要展示的字段
# 定义分类管理
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name']  # 展示name字段


# 定义博客管理
class BlogAdmin(admin.ModelAdmin):
    list_display = ['category', 'title', 'public_time', 'author']


# 定义评论管理
class CommentAdmin(admin.ModelAdmin):
    list_display = ['content', 'public_time', 'blog', 'author']


# 注册这三个模型管理模块
admin.site.register(Category, CategoryAdmin)
admin.site.register(Blog, BlogAdmin)
admin.site.register(Comment, CommentAdmin)
```

### 分页

- 一个简单的分页示例:views.py

```python
# 导入分页包
from django.core.paginator import Paginator
# 导入模板渲染包
from django.shortcuts import render
# 导入模型
from .models import Blog

def blog_list(request):
    blogs = Blog.objects.all().order_by('-created_at')  # 获取所有博客并按创建时间降序排序
    paginator = Paginator(blogs, 5)  # 每页显示5条记录
    page_number = request.GET.get('page')  # 获取当前页码
    page_obj = paginator.get_page(page_number)  # 获取当前页的记录

    return render(request, 'blog_list.html', {'page_obj': page_obj})
```

- `blog_list.html`中

```html
<!-- 遍历展示数据 -->
{% for blog in page_obj %}
<li>
  <h2>{{ blog.title }}</h2>
  <p>{{ blog.content|truncatewords:30 }}</p>
  <p>Created at: {{ blog.created_at }}</p>
</li>
{% endfor %}
<!-- 进行分页 -->
<div class="pagination">
  <span class="step-links">
    <!--    如果有上一页    -->
    {% if page_obj.has_previous %}
    <a href="?page=1">&laquo; 第一页</a>
    <a href="?page={{ page_obj.previous_page_number }}">上一页</a>
    {% endif %}

    <span class="current">
      第 {{ page_obj.number }} 页,共 {{ page_obj.paginator.num_pages }} 页
    </span>

    {% if page_obj.has_next %}
    <a href="?page={{ page_obj.next_page_number }}">下一页</a>
    <a href="?page={{ page_obj.paginator.num_pages }}">最后一页 &raquo;</a>
    {% endif %}
  </span>
</div>
```

### 验证码

- 这个 demo 实现验证码的逻辑是:
  - 1 前端输入邮箱
  - 2 点击页面获取验证码 ajax 异步请求视图层的验证码函数
  - 3 函数内,在验证码数据表创建一行(用户名:验证码)的数据,并用 smtp 发送邮件
  - 4 注册时,比对数据库的数据,成功完成注册后,删除数据库数据
    > 1,2 步参考`/static/js/register.js`
    > 3 步参考`/blogauth/views.py`中的`send_email_captcha(request)`方法
    > 4 步为参考`/blogauth/forms.py`中的`RegisterForm`类,里面有验证码函数,在视图层直接通过`form.is_valid()`来判断即可

### 搜索

- 唯一的知识点:Q 表达式的导入和写法
  - 导入:`from django.db.models import Q`
  - 写法:`blog_list = Blog.objects.filter(Q(title__icontains=keyword)|Q(content__icontains=keyword)).order_by('-id').all()`
    > `.filter(Q(字段=值) |表示或 Q(字段=值) )`,上面的那句话的意思就是返回`title字段__不区分大小写包含keyword` 的或者 content 字段相同规则的
    > !只有一个=号,注意!

### 插件:wangEditor.js 富文本编辑器

- 加载 demo

```html
<!-- 加载css,href里可以打开拷贝放本地,不再赘述 -->
<link
  href="https://unpkg.com/@wangeditor/editor@latest/dist/css/style.css"
  rel="stylesheet"
/>
<style>
  #editor—wrapper {
    border: 1px solid #ccc;
    z-index: 100; /* 按需定义 */
  }
  #toolbar-container {
    border-bottom: 1px solid #ccc;
  }
  #editor-container {
    height: 500px;
  }
</style>
...
<!-- 定义html结构,两个div -->
<div id="editor—wrapper">
  <div id="toolbar-container"><!-- 工具栏 --></div>
  <div id="editor-container"><!-- 编辑器 --></div>
</div>
...
<!-- 加载js,不在赘述 -->
<script src="https://unpkg.com/@wangeditor/editor@latest/dist/index.js"></script>
<!-- 这一部分直接在post_create.js中 -->
<script>
  const { createEditor, createToolbar } = window.wangEditor;

  const editorConfig = {
    placeholder: "Type here...",
    onChange(editor) {
      const html = editor.getHtml();
      console.log("editor content", html);
      // 也可以同步到 <textarea>
    },
  };

  const editor = createEditor({
    selector: "#editor-container",
    html: "<p><br></p>",
    config: editorConfig,
    mode: "default", // or 'simple'
  });

  const toolbarConfig = {};

  const toolbar = createToolbar({
    editor,
    selector: "#toolbar-container",
    config: toolbarConfig,
    mode: "default", // or 'simple'
  });
</script>
```

- 需要注意,wangEditor 必须异步 http 请求传送数据,详情见`./static/post_create.js`

### 插件:highlist.js 实现代码高亮

- 上文有,不再赘述
