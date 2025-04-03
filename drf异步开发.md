# drf 异步开发

- 本次学习 drf 实现并发编程, 主要依赖 `adrf` 库.

## 环境搭建

1. 新建django项目
2. code打开项目, `Ctrl + Shift + P`, 输入 `Python select Interpreter`, 创建虚拟环境...
3. 安装 `django`, `daphne`, `drf`, `adrf`

```bash
pip install django
pip install daphne
pip install django_rest_framework
pip install adrf
```

## 环境搭建注意

1. 创建虚拟环境后, 最好再设置一遍, 并 **重启** 编辑器, 确保是使用的虚拟环境的python
2. 如果不幸还是污染了全局环境, 可以依次执行下面的命令, 让 pip list 里只留下 pip

```bash
# 1, 获取当前环境(全局)的所有依赖并生成 .txt 文件
pip freeze > requirements.txt

# 2, 根据 .txt 文件删除所有依赖
pip uninstall -r requirements.txt -y
```

4. 配置 `settings.py`

```python
INSTALLED_APPS = [
    # 安装 daphne, rest_framewrok, adrf
    'daphne',
    'rest_framework',
    'adrf',

    # ...
]

# ...

# 配置 asgi 服务器
ASGI_APPLICATION = 'adrfdemo.asgi.application'
```

## 异步 APIView

```python
# 从 adrf.views 里导入的 APIView 将会是异步的
from adrf.views import APIView
from rest_framework.response import Response

# 类视图依旧继承 APIView, 但此刻已经继承的是异步的, 来自 adrf 里的 APIView
class IndexView(APIView):
    # 内部的函数就必须要添加 aysnc 关键字
    async def get(self, request):
        return Response({"message": "请求正常!"})
    
class MessageView(APIView):
    async def post(self, request):
        message = request.data.get('message')
        print(message)

        return Response({"message": f"你的消息'{message}'已收到"})
```

- 从 `adrf.views` 里导出 `APIView` 用于继承
- 声明函数时, 需要前面添加 `async` 关键字

## 异步视图集 ViewSet

```python
# 从 adrf.viewsets 中导入 ViewSet
from adrf.viewsets import ViewSet

# 继承 adrf.viewsets.ViewSet
class AsyncUseViewSet(ViewSet):

    # 方法全部要重写, 添加上 async
    async def list(self, request):
        return Response({"message": '假设你获取了所有用户'})
    
    # 方法全部要重写, 添加上 async
    async def retrieve(self, request, pk):
        return Response({"message": f'假设你获取了主键为{pk}的用户'})
```

- 继承 `adrf.viewsets.ViewSet`, 而不再是同步的 rest_framework.viewsets 里的
- 方法需要全部重写, 添加上 `async` 关键字

> `ModelViewSet` 同理, 如果想要三行代码搞定全部CURD操作, 可以直接继承 `from adrf.viewsets import ModelViewSet`

## 异步认证 authentication

```python
from rest_framework.authentication import BaseAuthentication, get_authorization_header
from django.contrib.auth import get_user_model
from rest_framework import exceptions

User = get_user_model()

class AsyncAuthentication(BaseAuthentication):
    # 模拟 JWT 认证
    keyword = 'JWT'

    # 这里一定要定义成异步函数, 前面加上 async 关键字
    async def authenticate(self, request):
        auth = get_authorization_header(request).split()

        token = auth[1].decode('utf-8')

        if token == 'ok':
            # 此处获取用户必须 await, afirst()获取第一个用户
            user = await User.objects.afirst()
            setattr(request, 'user', user)

            return user, token
        else:
            raise exceptions.ValidationError('JWT 验证失败')

# 别忘记执行迁移 python manage.py migrate

# 别忘记 settings.py 里声明全局验证
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': ['front.authentications.AsyncAuthentication']
}
```

- 此时再访问任何借口, 都需要请求头带 `Authorization = JWT ok`
- 实现异步认证, 还是正常继承`BaseAuthentication`, 然后 `async def authenticate(self, request):` 认证方法定义为异步
- 在内部如果有 I/O 操作(比如访问数据库), 就需要比如 `await ... Model.objects.afirst()` (await, afirst)

## 异步权限 permission

```python
import random

class AsyncPermission:
    # 这里声明 has_permission 函数为异步即可, 定义是固定写法:
    async def has_permission(self, request, view) -> bool:

        # 模拟: 必须小于0.7才可以访问
        if random.random() < 0.7:
            return False

        return True 

# 投入使用要么在视图里指定 permission_classes = [AsyncPermission]

# 或者在 settings.py 中声明全局投入使用
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': ['front.authentications.AsyncAuthentication'],
    # 全局都需要以下类通过鉴权: front模块.permissions文件.AsyncPermission类
    'DEFAULT_PERMISSION_CLASSES': ['front.permissions.AsyncPermission']
}
```

- 异步权限和异步认证一样, 就是定义方法时加上 `async` 关键字即可

## 异步限速节流 throttle

```python
from rest_framework.throttling import BaseThrottle
import random

# 同样, 正常继承
class AsyncThrottle(BaseThrottle):
    # 重写函数定义时加上 async 关键字
    async def allow_request(self, request, view):

        # 模拟节流
        if random.random() < 0.7:
            return False
        
        return True
    
    # 需要等待3秒才可再次访问
    def wait(self):
        return 3
    
# 投入使用同样, 要么针对单个视图限速节流: throttle_classes = [AsyncThrottle]
# 要么 settings.py 中配置全局生效
REST_FRAMEWORK = {
    # ...

    # 全局默认的限速节流
    'DEFAULT_THROTTLE_CLASSES': ['front.throttles.AsyncThrottle'],
}
```

- 同样, 正常继承, 声明函数加上 `async` 关键字即可实现异步限速节流

> 注意: drf并发编程中, 如果没有阻塞式的 I/O 操作(比如读写数据库), 不一定需要将所有的权限, 认证, 限速节流都定义为异步的, 甚至同步写法效率还会更高(因为若不存在阻塞, 此时同步和异步没有区别, 都需要从上到下执行, 而异步反而会因为开辟协程, 影响效率)

## 异步序列化器 serializer

### 上传的数据校验

- 注意: `adrf` 没有对 `validate_xxx`, `validate` 方法编写异步处理的逻辑, 因此自定义验证相关的方法只能是同步的, 从而不能在校验逻辑中出现阻塞式 I/O 操作(比如读写数据库)
- 上面说人话就是: 打个比方, 如果我们实现注册功能, 表单提交上来, 数据进序列化器, 如果是同步编程, 我们可以直接在序列化器里面声明 `validate()` 方法访问数据库, 看看用户存不存在, 邮箱手机是否重复等等, 但如果异步编程, 在这里执行这些操作就不行了, 请在异步视图层完成操作, 异步的序列化器只能完成字段合法性的校验, 不能进行数据库比对操作

### ORM 转字典

- 序列化器正常写
- 获取最终数据时, 需要 `await 序列化器.adata`

### 代码示例

```python
########## 序列化器 ##########
# 导入 adrf.serializers.Serializer, 而非 rest_framework 里的
from adrf.serializers import Serializer
from rest_framework import fields

# 
class AsyncLoginSerializer(Serializer):
    """
    登录序列化器: 验证用户上传的数据
    """
    username = fields.CharField(max_length=20)
    password = fields.CharField(min_length=6, max_length=20)

    # 必须是同步的! 且内部禁止任何 I/O 操作!
    def validate(self, attrs):
        username = attrs['username']
        if username != 'liudehua':
            raise fields.ValidationError('用户名必须是刘德华!')
        
        return attrs
    
class UserSerializer(Serializer):
    """
    格式化信息: 将ORM对象转为字典
    """
    username = fields.CharField(max_length=200)
    email = fields.EmailField()
    is_active = fields.BooleanField()
    is_staff = fields.BooleanField()

########## 视图层 ##########
from adrf.views import APIView
from .serializers import AsyncLoginSerializer, UserSerializer
# 这里要导入异步的验证方法 aauthenticate 
from django.contrib.auth import aauthenticate

class AsyncLoginView(APIView):
    """
    模拟登录接口
    """
    async def post(self, request):
        # 序列化器1: 验证数据
        serializer = AsyncLoginSerializer(data=request.data)

        if serializer.is_valid():
            username = serializer.validated_data['username']
            password = serializer.validated_data['password']

            # 在异步视图中再进行 I/O 操作: 访问数据库, 验证用户名和密码, 这里需要使用 a!authenticate
            user = await aauthenticate(request, username=username, password=password)

            if user:
                # 将用户信息序列化(ORM对象转字典)
                user_serializer = UserSerializer(user)
                # 获取最终信息: await 序列化器.adata
                logined_user = await user_serializer.adata

                return Response({"message": '登录成功', "user": logined_user})
            else:
                return Response({"message": '表单验证成功, 但登录失败(数据库里没有用户信息)'})
        else:
            error_msg = list(serializer.errors.values())[0][0]
            return Response({"message": error_msg})
```

- 疑问?: `user = await aauthenticate(request, username=username, password=password)` 如果验证通过, user 就已经在内存中了, 为何获取最终需要仍要异步: `logined_user = await user_serializer.adata`
- 回答!: 因为如果存在序列化器嵌套, 仍旧需要异步查询数据库, 才可以获取嵌套的数据, 比如序列化器里万一还有个"用户所属部门"字段, 就需要异步查询数据库, 所以这里必须是异步的, 否则会造成阻塞

### 查找列表

```python
# 导包略...

class AsyncUseViewSet(ViewSet):
    async def list(self, request):
        # 配置queryset
        queryset = User.objects.all()
        # 放入序列化器, 声明有多条数据
        serializer = UserSerializer(queryset, many=True)
        # 真正进行查找: await .adata
        users = await serializer.adata

        return Response({"users": users})
```

> 模型序列化器同样支持 `adrf.serializers.ModelSerializer`