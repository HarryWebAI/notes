# django 异步

## ASGI 服务器

- 以前同步开发, 可以使用 WSGI 协议的服务器运行项目, 比如 `uwsgi`

- 现在开发异步, 就要使用 ASGI 协议的服务器运行项目, django 官方推荐 3 个
  - `Daphne` (开发环境建议使用)
  - `Uvicorn` (生产环境建议使用)
  - Hypercorn

## Daphne

- 安装: `pip install dephne`
- 投入使用: 创建好 django 项目(本次学习取名: myproject)后, 在 `settings.py` 中配置:

```python
INSTALLED_APPS = [
    # 将 daphne 投入使用 (建议放在最上面)
    'daphne',
]

# 声明 ASGI 服务器
ASGI_APPLICATION = 'myproject.asgi.application'
```

- 测试, 输入命令: `python manage.py runserver`, 项目正常启动, 命令行显示一下数据, 说明异步服务器成功投入使用

```bash
Starting ASGI/Daphne version 4.1.2 development server at http://127.0.0.1:8000/
```

## 虚拟环境创建 django 项目并将 dephne 投入使用

1. 全局安装 django: `pip install django`
2. 创建项目 `django-admin startproject <项目名称>`
3. `Ctrl + Shift + P` 打开命令面板, 输入 `Python: Select Interpreter` 选择解释器
4. 建议创建虚拟环境, 它会在当前项目下生成一个 `.venv` 文件
5. 再次重复第一步, 确保当前项目的 python 是采用的虚拟环境(.venv里的python), 不会影响影响全局
6. 再安装一遍django

- 全局安装django是为了命令 `django-admin startproject`
- 同时为了一个项目一个虚拟环境, 不污染全局, 我们又需要创建虚拟环境
- 创建的虚拟环境又没有 django, 只好再装一遍

> 这是目前我想到的使用 vsCode 写 python 的解决方式

- 装 daphne 和 mysqlclient : `pip install daphne`, `pip install mysqlclient`

## 异步视图

- 先要在 `settings.py` 中声明模板存放路径

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # 这里需要添加声明, 告知django模板存放路径
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

- 定义异步视图只需要在函数前面加上 `async` 关键字即可

```python
from django.shortcuts import render
from django.views import View

# 定义异步函数视图 async 关键字
async def index(request):
    return render(request, 'index.html')

# 定义异步类视图
class AboutView(View):
    # 这里添加 async 关键字
    async def get(self, request):
        return render(request, 'about.html')
```

## 异步 ORM

- 数据库操作是一个典型的可以用异步优化的模块, 在异步 ORM 中, 存在两种操作:
  - 返回 QuerySet 的方法
  - 不返回 QuerySet 的方法

### 返回 QuerySet 的方法

- 在 django 中, QuerySet 对象是 "懒惰" 的, 他只是构建一个查询对象, 不会真正地执行 SQL 语句, 只有对其执行以下操作时, 才会真正地访问数据库
  - 迭代, 循环
  - 切片, 当步长不是1时, 就会执行 SQL 语句
  - Pickle序列化 / 缓存
  - 使用 repr 函数
  - 使用 len 获取元素数量
  - 使用 list 强制转列表时
  - 使用 bool 函数, 或者 if 判断时
- 所以在异步模式中, 不能直接使用以上操作, 否则将运行阻塞的同步代码, 这种情况的处理方式, 是通过 **`异步迭代`** 的方式来查找数据库, demo 如下:

```python
# 配置 queryset 目标是获取所有 book
queryset = Book.objects.all()

# 创建空列表
books = []

# 以异步迭代的方式遍历queryset, 获取所有 book 的异步数据
async for book in queryset:
    books.append(book)
```

> 使用 `async for` 获取

### 不返回 QuerySet 的方法

- first
- save
- delete 等...
- django 针对以上的方法大多提供了异步版本, 只需要前面加上`a`即可, 比如
- `await obj.afirst()`
- `await obj.asave()`
- `await obj.adelete()`

### 事务暂不支持异步模式

- django 官方的ORM事务暂时不支持, 后续项目再学习解决方案

## 异步表单

- django.forms 本身并不支持异步I/O操作, 想要实现异步有两种解决方案:
  - 要么在视图层去执行数据库的访问
  - 要么使用异步适配函数 sycn_to_asycn
- 举例

```python
########## 表单 ##########
# 想要实现异步 I/O 操作, 表单里是不行的, 目前有两种解决方法:
# 1, 把查找数据库的操作放在视图中
# 2, 在表单中写同步 I/O 操作, 在异步视图中使用 sync_to_async(), 将表单的验证操作转为异步的
class AddBookForm(forms.Form):
    name = forms.CharField(min_length=2, max_length=200)
    author = forms.CharField(min_length=2, max_length=200)`
    price = forms.FloatField()

    def clean_name(self):
        name = self.cleaned_data.get('name')
        if Book.objects.filter(name=name).exists():
            raise forms.ValidationError('图书已存在!')


########## 视图 ##########
from django.shortcuts import render
from .forms import AddBookForm
from django.http.response import HttpResponse

# 导入异步适配函数
from asgiref.sync import sync_to_async

async def add_book(request):
    form = AddBookForm(request.POST)

    # 在这里将同步的表单验证函数转为异步的
    ais_valid = sync_to_async(form.is_valid)

    # 调用异步的表单验证函数, 注意 await 关键字
    if await ais_valid():
        name = form.cleaned_data.get('name')
        author = form.cleaned_data.get('author')
        price = form.cleaned_data.get('price')

        print(name, author, price)

        return HttpResponse("表单验证成功")
    else: 
        print(form.errors)

        return HttpResponse("表单验证失败")
```

## 完整的demo

- 异步实现图书的新增, 详情, 列表

```python
########## 视图 ##########
from django.shortcuts import render, redirect
from django.urls import reverse
from .forms import AddBookForm
from asgiref.sync import sync_to_async
from django.http.response import HttpResponse
from .models import Book
from django.views import View

async def add_book(request):
    """
    添加图书
    """
    if request.method == 'GET':
        return render(request, 'add_book.html')
    if request.method == 'POST':
        form = AddBookForm(request.POST)

        ais_valid = sync_to_async(form.is_valid)

        if await ais_valid():
            name = form.cleaned_data.get('name')
            author = form.cleaned_data.get('author')
            price = form.cleaned_data.get('price')

            # 方法1:
            # book = Book(name=name, author=author, price=price)
            # await book.asave()

            # 方法2:
            book = await Book.objects.acreate(name=name, author=author, price=price)

            # 路由重定向(路由反转('反转book模块下的book_detail路由', 参数={"pk": 新建的book.id}))
            return redirect(reverse('book:book_detail', kwargs={"pk": book.id}))
        else: 
            print(form.errors)

            return HttpResponse("表单验证失败")

async def book_detail(request, pk):
    """
    图书详情
    """
    try:
        book = await Book.objects.aget(pk=pk)
    except Exception as e:
        print(e)

    return render(request, 'book_detail.html', context={"book": book})

class BookListView(View):
    """
    图书列表
    """
    async def get(self, request):
        queryset = Book.objects.all()

        books = []

        async for book in queryset:
            books.append(book)

        return render(request, 'book_list.html', context={"books": books})


########## 路由 ##########
from django.urls import path
from . import views

app_name = 'book'

urlpatterns = [
    path('add', views.add_book, name='add_book'), # 添加
    path('detail/<int:pk>', views.book_detail, name='book_detail'), # 详情
    path('', views.BookListView.as_view(), name='book_list'), # 列表
]

########## 模型和模板, 略 ##########
```

- 添加时, 因为表单不支持异步, 所以数据验证就用 `ais_valid = sync_to_async(form.is_valid)` 将验证函数转为异步的
- 异步获取详情: `await Book.objects.aget(pk=pk)` (await aget)
- 异步获取列表详情: 异步迭代 (`async for`)

## 异步适配函数

- django 官方提供 `asgiref` 包实现异步适配

### 同步转异步

- `sync_to_asycn(函数名, thread_sensitive=True)`
- 第一个参数传入同步函数函数名, 第二个叫做"线程是否敏感", 默认为 True
- 本质上就是将同步函数放到新的线程中执行, 这样就不会造成当前线程阻塞.
- `thread_sensitive`, 默认为 True, 即该请求的所有同步函数在同一个线程(非主线程)中执行
- 如为 False, 代表该同步函数在一个独立的线程中执

> 如果多个同步函数需要访问相同的临界值, 那么就使用默认的方式, 这种方式会让当前所有同步函数运行在同一个线程中, 不会对临界值造成问题
>
> 如果多个同步函数间不会访问临界值, 以及同步函数可能比较耗时时, 可以用 False, 因为这种方式会将同步函数放在新线程里执行, 可以提高并发性

### 异步转同步

- `async_to_sycn(异步函数名, force_new_loop=False)`
- 第一个参数传入异步函数函数名, 第二个叫做"是否强制创建新循环"
- 这个函数将会让异步函数作为同步函数执行, 如果当前线程有事件循环, 则会直接将异步函数加入到事件循环中执行, 如果没有, 则会创建一个事件循环, 再将该异步函数加入到事件循环中执行. 同步函数会等待事件循环执行完毕后再继续往下执行.

> 以上两个函数只是转换函数的执行方式, 转换完了还需要自己手动执行, 转为异步了别忘记 `await async_func`

## 异步中间件

- 异步开发中, 中间件必须是异步的, 如果中间件是同步的, django会先通过线程的方式切换到同步模式执行中间件代码, 再切换到异步模式来执行视图函数代码, 这样一来就会抵消异步编程带来的性能提升

### 函数中间件

- 函数中间件可以采用以下三个装饰器实现
  - `sync_only_middleware`      => 仅同步的中间件
  - **`async_only_middleware`** => 仅异步的中间件(真正用的  )
  - `sync_and_asycn_middleware` => 同时支持异步和同步
- 示例

```python
from asgiref.sync import iscoroutinefunction
from django.utils.decorators import sync_and_async_middleware, sync_only_middleware, async_only_middleware

@sync_and_async_middleware
def simple_middleware(get_response):
    # 判断 get_response 是否是协程函数(是否是异步的)
    if  iscoroutinefunction(get_response):
        # 是异步的, 中间件就必须是异步的
        async def middleware(request):
            print('视图执行前, 调用了异步中间件...')
            # 在返回视图前还可以做其他事...

            # 异步的中间件, 就需要 await
            response = await get_response(request)

            print('视图执行后, 调用了异步中间件!')
            # 在返回视图后还可以做其他事...

            return response
    else:
        # 否则中间件就是同步的
        def middleware(request):
            print('视图执行前, 调用了同步中间件...')

            response = get_response(request)

            print('视图执行后, 调用了同步中间件!')

            return response
        
    return middleware
```

- 注意装饰器从哪里导入的,
- 无论同步还是异步, 函数中间件的写法都如上所示: 在调用 `get_response` 前后做业务逻辑即可
- 声明了中间件, 别忘记在 `settings.py` 中注册

### 类中间件

- 代码示例

```python
from asgiref.sync import iscoroutinefunction
# 多导一个包: 标记为协程
from asgiref.sync import markcoroutinefunction
from django.utils.decorators import sync_and_async_middleware, sync_only_middleware, async_only_middleware

# 同样需要装饰器
@async_only_middleware
class AsyncMiddleware:
    # 允许异步
    async_capble = True
    # 禁用同步
    sync_capble = False

    # 构造函数的固定写法
    def __init__(self, get_response):
        self.get_response = get_response

        # 判断是否是协程
        if iscoroutinefunction(self.get_response):
            # 标记为协程
            markcoroutinefunction(self)

    # 定义异步中间件
    async def __call__(self, request):
        print('异步中间件: 在视图执行前...')

        # 反复强调, 这里别忘了 await
        response = await self.get_response(request)

        print('异步中间件: 在视图执行后!')

        return response
```

### 异步装饰器

- django 5.x 版本后, 很多装饰器(decorator)都支持异步了, 可以直接投入使用
