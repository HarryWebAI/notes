# 什么是 DRF

- Django Rest Framework,是 Django 框架中,实现 Restful API 的一个插件,可以非常方便实现接口数据返回.
- 普通 Django 中,也可以使用 JsonResponse 直接返回 JSON 格式的数据,但 DEF 具有以下好处:
  1. 自动生成 API 文档
  2. 授权验证策略完成,包含 OAuth1 和 OAuth2 验证
  3. 支撑 ORM 模型和非 ORM 模型的序列化
  4. 高度封装视图,使返回 JSON 数据更高效

# 安装 DRF

- 命令 `pip install djangorestframework`
- 指定版本安装`pip install djangorestframework==版本号`

# 准备开始学习

### 新建项目,新建数据库,配置

- 新建 django 项目`django_drf_learn`,略
- 打开`~/django_drf_learn/settings.py`,配置数据库

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'drflearn',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': '...',
        'PASSWORD': '...'
    }
}
```

- 新建一个数据库`drflearn`,略

### 了解 Postman

- 一款用于测试 API 的软件
- <a href="https://www.postman.com/downloads/">下载地址</a>
- 安装完成后要邮箱完成注册略
- 主要使用:"Collections", "+"号, 新增"Blank Collection"(空的集合)
- 这个 Collection 可以对应一个项目,[Ctrl+e]修改 Collection 为项目名称
- 这个集合下,可以新建`Add request`很多 Http 请求,输入指定的 url,配置好参数,postman 将模拟请求,访问我们写好的 api 并获取返回的 json 数据,以此我们能够确定我们写的接口能不能用,返回的数据格式是什么样的

### 创建模拟 app 以及模型

- `工具Tools`,`运行Manage.py任务`(快捷键 Ctrl+Alt+R),以此打开的控制台,执行`python manage.py [要执行的命令]`时,就不用再输入前面的`python manage.py`了
- 创建 app`startapp meituan`
- 编辑模型`~/meituan/models.py`
- 在`settings.py`中挂载 app

```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # 挂载app:美团和drf
    "meituan",
    "rest_framework"
]
```

- 创建迁移文件`make migrations`并且执行迁移`migrate`
- 写入假的 sql 语句:打开 My Sql Workbench,选中`drflearn`数据库里的`tables`,再点击左上角的`Files`,选择`Run SQL Script`,导入一个.sql 文件并执行
- 现在我们的 django 框架加载了 meituanApp 和 rest_frameworkAPP,
- 并且创建了模型商品,商品种类,商家,
- 并且写入了很多条假数据,用于之后学习

# DRF 快速入门

### 准备工作

- 如果项目准备全部采用 django_rest_framework_API 的形式开发,那么我们应该关闭`csrf`这个中间件,`settings.py`

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware', #禁用csrf
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

### 序列化,视图,路由

- 新建一个只需要序列化:`serializer.py`, 视图:`views.py`和路由`urls.py`的 App
- serializer 序列化映射模

```python
# 导包
from rest_framework import serializers
from meituan.models import Merchant

# 新建序列化对象
class MerchantSerializer(serializers.ModelSerializer):
    class Meta:
        # 指定模型
        model = Merchant
        # 指定字段
        fields = "__all__" # __all__表示所有
        # 或者指定不需要的字段
        # exclude =
```

- views 视图完成方法,两句话包含增删改查

```python
#导包
from rest_framework import viewsets
from meituan.models import Merchant
from .serializer import MerchantSerializer

# 这个视图函数已经包含了增,删,改,检索
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class =MerchantSerializer
```

- ulrs 配置路由

```python
# 导包
from rest_framework.routers import DefaultRouter
from .views import MerchantViewSet

# 新建路由对象
router = DefaultRouter()
# 告诉路由,你要映射的是上面的视图
router.register('merchant', MerchantViewSet, basename='merchant')

# 配置app名称
app_name = 'quickstart'

# 把路由加在后面即可
urlpatterns = [] + router.urls
```

- 主 urls.py 里面 include 该路由

```python
from django.contrib import admin
from django.urls import path,include # 导入include函数

urlpatterns = [
    path('admin/', admin.site.urls),
    path('meituan/', include('quickstart.urls')) # 配置该路由,地址为meituan/
]
```

### Postman 模拟请求

- 在上面简单的操作后,我们就有了一个指向`localhost/meituan/merchant`的路由,和路由映射的相应视图,以及视图内所带的序列化模型,序列化模型自带的一系列方法
- 通过不同的请求方法,用 Postman 请求该路由,我们会得到不同的结果:
- `GET`检索
  - 直接请求`localhost/meituan/merchant`我们会得到所有数据
  - 请求`localhost/meituan/merchant/id`我们会得到指定某一条数据
- `POST`增加
  - 编辑请求体`body`,选择`x-www-form-urlencoded`,填写相关表单数据
  - 然后直接请求该路由,会实现添加功能(相当于弄了个假表单 post 给了服务器),视图函数将自动识别 post 请求为添加
- `PUT`修改
  - 同样,编辑请求体(假表单)传送给服务器
  - 但是请求的路由必须包含`id`,即`localhost/meituan/merchant/id`告诉服务器我们要修改哪一条数据
- `DELETE`删除
  - `DELETE`请求`localhost/meituan/merchant/id`,服务器删除指定 id 数据,并且返回状态码`204`表示删除成功

# 序列化

### 一个标准的序列化文件

```py
from rest_framework import serializers
from meituan.models import Merchant

# 1, 用来序列化数据, 就是将ORM模型models.py转换为JSON字符串
# 2, 用来验证表单数据, forms.py
# 3, 可以增,改数据

class MerchantSerializer(serializers.Serializer):
    # 序列化数据,并且声明验证规则
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField(max_length=200, required=True)
    logo = serializers.CharField(max_length=200, required=True)
    address = serializers.CharField(max_length=200, required=True)
    notice = serializers.CharField(max_length=200, required=False)
    up_send = serializers.DecimalField(required=False, max_digis=6, decimal_places=2)
    lon = serializers.FloatField(required=True)
    lat = serializers.FloatField(required=True)

# 重写底层的 serializer.py 里的方法
# 新增
    def create(self, validated_data):
        # validated_data{name: '张三', ...},
        # 加上**变成关键字参数,执行的内容其实是Merchant.objects.create(name='张三', ...)
        return Merchant.objects.create(**validated_data)
    # 修改
    def update(self, instance, validated_data):
        instance.name = validated_data.get('name', instance.name)
        instance.logo = validated_data.get('logo', instance.logo)
        instance.address = validated_data.get('address', instance.address)
        instance.notice = validated_data.get('notice', instance.notice)
        instance.up_send = validated_data.get('up_send', instance.up_send)
        instance.lon = validated_data.get('lon', instance.lon)
        instance.lat = validated_data.get('lat', instance.lat)

        instance.save()
        return instance
```

> 踩坑: Postman 发送 POST 请求后报错`create() must be implemented.`?create()函数未声明?但我已经重写声明了这两个函数啊?

> 我真蠢, create(), update()两个方法,不是和 MerchantSerializer 类一级的,而是写在这个类里面的!

### 映射在视图函数上,并配置相关路由

- `views.py`

```py
from .serializer import MerchantSerializer
from django.http import JsonResponse
from django.views.decorators.http import require_http_methods
from meituan.models import Merchant

@require_http_methods(['GET', 'POST'])
def merchant(request):
    # GET 返回所有商家
    if request.method == 'GET':
        queryset = Merchant.objects.all()
        serializer = MerchantSerializer(instance=queryset, many=True) #queryset用于查询数据库后序列化为JSON
        # many=True: 意为查询多个
        return JsonResponse(serializer.data, safe=False)
        # safe=False, 传递非字典对象时
    # POST 创建新的商家
    if request.method == 'POST':
        serializer = MerchantSerializer(data=request.POST) # data验证数据数据合规
        if serializer.is_valid(): # 和 django.form 很像,
            serializer.save() # 直接调用.sava()方法,其底层会调用我们重写的create()方法
            return JsonResponse(serializer.data, status=200) # 200成功
        else:
            return JsonResponse(serializer.errors, status=400) # 400服务端错误
```

- app 里的`urls.py`配置好,和主路由 include() 进去,略

### 常见的状态码

- `200` => 成功
- `400` => 失败(请求成功了,但访问不进去,应该是访问的方式,比如提交的表单不正确)
- `404` => 未找到(服务器没有这个路由)
- `500` => 服务器端错误(通常是服务器代码写错了)

### 模型序列化

- <<快速入门>>章节中,我们用了一个`class Meta`类简写了上面的代码

```py
class MerchantSerializer(serializers.ModelSerializer):
    class Meta:
        model = Merchant
        fields = "__all__"
```

- 就这么简单,不用重写方法,声明那么多字段,因为相当于 Meta 类直接把模型拿了过来
- serialize 简直和 django.forms 太像了

### 序列化嵌套

- 和模型(数据表)的关系很像,1 个分类对应 1 个商家,1 个分类下对应 n 个商品,我们如何在序列化中体现?

```py
class GoodsCategorySerializer(serializers.ModelSerializer):
    # 序列嵌套-1个商品分类对应1个商家
    merchant = MerchantSerializer(read_only=True)
    # 写入存在问题, 上面简单嵌套后,要求写入时, 字段merchant不能只写id, 如何处理?
    # 设置一个新的字段, merchant_id, 只可写入
    merchant_id = serializers.IntegerField(write_only=True)

    # 序列嵌套-1个商品分类对应n个商品
    goods_list = GoodsSerializer(many=True, read_only=True)

    class Meta:
        model = GoodsCategory
        fields = "__all__"

    # 验证商家是否存在
    def validate_merchant_id(self, value):
        if Merchant.objects.filter(pk=value).exists():
            return value
        else:
            raise serializers.ValidationError("商家不存在")

    # 重写create()方法
    def create(self, validated_data):
        merchant_id = validated_data.get('merchant_id')
        merchant = Merchant.objects.get(pk=merchant_id)
        category = GoodsCategory.objects.create(**validated_data, merchant=merchant)
        return category
```

- 需要注意的第一点:1 对 1 关系中,检索时,我们可以不管对应表的 id,但**新增时必须处理**:
  - 新增一个`对应表_id`字段并验证,最后重写`create()`方法,写入信息
- 而在 1 对 n 关系中,检索时,必须要声明`many=True`

# DRF 的 Request 和 Response

- 思考:django 本身自带,为什么还用 DRF 的呢?

### Request

- 一般的 Django 获取表单数据`request.POST`
- drf 却可以通过`request.data`进行获取,包括 POST, PUT, PATCH 等其他方式传来的数据
- `request.query_params`: 获取查询参数,比`request.GET`更直白
- 想要使用该对象,不需要任何其他操作,相当于 DRF 帮我们重写了 Django 自带的 request

### Response

- 想要使用 Response 对象,需要导入`from rest_framework.response import Response`
- 同时如果使用 Response 返回数据,那么不能用 Django 原本的装饰器限制访问方式,而应该使用`api_view`
  - 导入:`from rest_framework.decorators import api_view`
  - 使用:`@api_view(['GET', 'POST'])`
- 此时再访问,返回的 JSON 将比 Django.JsonResponse()返回更加美观:
  - 既显示了访问方式
  - 又显示了访问路由
  - 还自带状态码,以及更美观的 json 数据(不再是一长串字符串)

# 类视图

- 示例代码

```python
from rest_framework.views import APIView
from meituan.models import Merchant
from django.http import Http404
from .serializers import MerchantSerializer
from rest_framework.response import Response
from rest_framework import status
from rest_framework import generics
from rest_framework import mixins
from rest_framework import viewsets
from rest_framework.decorators import action


###################### APIView的代码 ######################
# class MerchantView(APIView):
#     """
#     检索, 更新和删除一个merchant实例对象.
#     """
#     def get_object(self, pk):
#         try:
#             return Merchant.objects.get(pk=pk)
#         except Merchant.DoesNotExist:
#             raise Http404
#
#     def get(self, request, pk=None):
#         if pk:
#             merchant= self.get_object(pk)
#             serializer = MerchantSerializer(merchant)
#             return Response(serializer.data)
#         else:
#             queryset = Merchant.objects.all()
#             serializer = MerchantSerializer(instance=queryset,many=True)
#             return Response(serializer.data)
#
#     def put(self, request, pk):
#         merchant = self.get_object(pk)
#         serializer = MerchantSerializer(merchant, data=request.data)
#         if serializer.is_valid():
#             serializer.save()
#             return Response(serializer.data)
#         return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
#
#     def delete(self, request, pk):
#         merchant= self.get_object(pk)
#         merchant.delete()
#         return Response(status=status.HTTP_204_NO_CONTENT)
###################### APIView的代码 ######################


###################### Mixin的代码 ######################
# class MerchantView(
#     generics.GenericAPIView,
#     mixins.ListModelMixin,
#     mixins.RetrieveModelMixin,
#     mixins.CreateModelMixin,
#     mixins.UpdateModelMixin,
#     mixins.DestroyModelMixin
# ):
#     queryset = Merchant.objects.all()
#     serializer_class = MerchantSerializer
#
#     def get(self,request,pk=None):
#         if pk:
#             return self.retrieve(request)
#         else:
#             return self.list(request)
#
#     # def perform_create(self, serializer):
#     #     serializer.save(created=self.request.user)
#
#     def post(self,request):
#         # 如果想要更改添加的逻辑,那么应该重写perform_create方法
#         return self.create(request)
#
#     def put(self,request,pk):
#         return self.update(request,pk)
#
#     def delete(self,request,pk):
#         return self.destroy(request,pk)
###################### Mixin的代码 ######################


###################### generic的代码 ######################
class MerchantView(
    generics.CreateAPIView,
    generics.UpdateAPIView,
    generics.DestroyAPIView,
    generics.RetrieveAPIView
):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer
    # lookup_field = 'name'


class MerchantListView(
    generics.ListAPIView
):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer


###################### generic的代码 ######################


###################### 视图集的代码 ######################
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer

    # list,retrieve,update,destroy

    @action(['GET'], detail=False, url_path='cs')
    def changsha(self, request):
        queryset = self.get_queryset()
        result = queryset.filter(name__contains='长沙')
        serializer_class = self.get_serializer_class()
        serializer = serializer_class(result, many=True)
        return Response(serializer.data)
###################### 视图集的代码 ######################
```

### 拆解代码

- 上面的代码从繁到简,重写了 APIView, Mixin, generics 的代码
- `APIView` 最复杂, 是所有类的父类, 所以直接继承它的时候, 需要自己写增删改查的逻辑
- `Mixin` "混入", "组件", 封装了 APIView
- `generics` "泛型", 再封装了 Mixin
  1. generics.ListAPIView:实现获取列表的。实现 get 方法。
  2. generics.CreateAPIView:实现创建数据的。实现 post 方法。
  3. generics.UpdateAPIView:实现更新数据的。实现 put 方法。
  4. generics.DestroyAPIView:实现删除数据的。实现 delete 方法。
  5. generics.RetrieveAPIView:实现检索数据的。
  6. generics.ListCreateAPIView:实现列表和创建数据的。
  7. generics.RetrieveUpdateAPIView:实现检索和更新数据的。
  8. generics.RetrieveDestroyAPIView:实现检索和删除数据的。
  9. generics.RetrieveUpdateDestroyAPIView:实现检索和更新和删除数据的。
- **重点** : `ViewSet` 视图集, 最终封装, 路由只需要一条

  - `views.py`

  ```python
    class MerchantViewSet(viewsets.ModelViewSet):
        queryset = Merchant.objects.all()
        serializer_class = MerchantSerializer
  ```

  - `urls.py`

  ```py
    from django.urls import path
    from .views import MerchantViewSet
    from rest_framework.routers import DefaultRouter

    app_name = 'classview'

    urlpatterns = []

    router = DefaultRouter()
    router.register('shangjia',MerchantViewSet,basename='shangjia')
    urlpatterns += router.urls
  ```

  - 通过导入`DefaultRouter`后,`router.register`注册路由,最后拼接进`urlpatterns`列表
  - 如果还想增加其他功能, `views.py`导入装饰器,并且在视图集类中声明

  ```py
  # 导入action
  from rest_framework.decorators import action
  ...
    @action(['GET'], detail=False, url_path='sc') #第一参数:请求方式, 第二参数:url带不带id, 第三参数方法url别名
        # 访问 /app/sc/ 即获取名称包含四川的所有数据,不设置url_path,就是访问 ../sichuan
        def sichuan(self, request):
            queryset = self.get_queryset() # 获取queryset
            result = queryset.filter(name__contains='四川') # 通过queryset模糊查询名称带四川的数据
            serializer_class = self.get_serializer_class() # 获取序列化
            serializer = serializer_class(result, many=True) # 序列化转换result为json数据
            return Response(serializer.data) # 返回给前端
  ```

# 认证和权限

### 认证逻辑

- 认证可以简单的理解为登录和访问需要登录的接口的认证。只要认证通过了,那么在 request 对象(是 drf 的 request 对象)上便有两个属性,一个是 request.user,一个是 request.auth,
  前者就是 django 中的 User 对象, 后者根据不同的认证机制有不同的对象。DRF 内置了几个认证的模块。以下进行简单了解。
  - `rest_framework.authentication.BasicAuthentication`:基本的授权。每次都要在 Header 中把用户名和密码传给服务器,因此不是很安全,不能在生产环境中使用。
  - `rest_framework.authentication.SessionAuthentication`:基于 django 的 session 机制实现的。如果前端部分是网页,那么用他是可以的,如果前端是 iOS 或者 Android 的 app,用他就不太方便了(如果要用也是完全可以的)。
  - `rest_framework.authentication.TokenAuthentication`:基于 token 的认证机制。只要登录完成后便会返回一个 token, 以后请求一些需要登录的 api,就通过传递这个 token 就可以了,并且这个 token 是存储在服务器的数据库中的。但是这种 token 的方式有一个缺点,就是他没有自动过期机制,一旦登录完成后,这个 token 是永久有效的,这是不安全的。

### JSON Web Token(JWT)

- JSON Web Token 简称 JWT。在前后端分离的项目中, 或者是 app 项目中,推荐使用 JWT。JWT 是在登录成功后,把用户的相关信息(比如用户 id)以及过期时间进行加密,然后生成一个 token 返回给客户端,客户端拿到后可以存储起来,以后每次请求的时候都携带这个 token,服务器在接收到需要登录的 API 请求后,对这个 token 进行解密,然后获取过期时间和用户信息(比如用户 id),如果过期了或者用户信息不对,那么都是认证失败。

- 想要在项目中使用,需要安装 <a href="https://pypi.org/project/PyJWT/">PyJWT</a> : `pip install PyJWT`

- 下面是一个 JWT 的简单演示

```py
# views.py
import jwt
import datetime
from django.conf import settings
from django.contrib.auth.models import User
from django.http import JsonResponse
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status

# 登录接口,生成 JWT Token
@api_view(['POST'])
def login(request):
    username = request.data.get('username')
    password = request.data.get('password')

    # 验证用户名和密码
    user = User.objects.filter(username=username).first()
    if user and user.check_password(password):
        # 认证成功,创建 JWT Token
        payload = {
            "sub": user.username,  # 用户名
            "role": "admin" if user.is_superuser else "user",  # 根据用户权限确定角色
            "exp": datetime.datetime.utcnow() + datetime.timedelta(hours=1)  # 1小时过期时间
        }

        # 生成 JWT
        token = jwt.encode(payload, settings.SECRET_KEY, algorithm="HS256")
        return JsonResponse({"token": token})

    return JsonResponse({"message": "Invalid credentials"}, status=status.HTTP_401_UNAUTHORIZED)

# 假设这是一个只有admin可以访问的接口
@api_view(['GET'])
def protected(request):
    # 获取 Authorization 头中的 JWT
    token = request.headers.get('Authorization')
    if not token:
        return JsonResponse({"message": "JWT令牌不存在"}, status=status.HTTP_403_FORBIDDEN)

    try:
        # 去掉 "Bearer " 前缀
        token = token.split(" ")[1]

        # 解码并验证 JWT
        payload = jwt.decode(token, settings.SECRET_KEY, algorithms=["HS256"])

        # 获取用户角色进行权限验证
        if payload["role"] != "admin":
            return JsonResponse({"message": "你没有权限访问此接口"}, status=status.HTTP_403_FORBIDDEN)

        return JsonResponse({"message": f"欢迎 {payload['sub']}! 您是 {payload['role']} 角色."})

    # exp时间过期
    except jwt.ExpiredSignatureError:
        return JsonResponse({"message": "令牌已过期!"}, status=status.HTTP_401_UNAUTHORIZED)
    # jwp认证失败
    except jwt.InvalidTokenError:
        return JsonResponse({"message": "令牌认证失败!"}, status=status.HTTP_403_FORBIDDEN)
```

- 总结:
  - `生成 JWT`:我们在用户登录后,生成一个包含用户名、角色和过期时间的 JWT 并返回给客户端。
  - `权限验证`: 通过 JWT 中的角色信息,后端可以验证用户是否有权限访问某些资源。
  - `JWT 验证`: 每次请求时,后端会验证 JWT 是否有效(包括签名和过期时间),并根据 JWT 中的角色信息进行权限控制。

### 权限

- DRF 自带四种权限:

  - `permissions.AllowAny`:允许所有人访问。
  - `permissions.IsAuthenticated`:是登录的用户即可访问(判断条件是 request.user and request.user.is_authenticated)
  - `permissions.IsAdminUser`:是管理员。(判断条件是 request.user and request.user.is_staff)
  - `permissions.IsAuthenticatedOrReadOnly`:是登录的用,并且这个 API 是只能读的(也就是 GET、OPTIONS、HEAD)。

- 当以上四种权限不够满足我们复杂的逻辑时,我们需要自定义权限类`~/appName/permissions.py`

```py
from rest_framework.permissions import BasePermission # 先要导入这个类用于继承

class IsAdminOrAuthor(BasePermission): # 配置类规则
    """
    仅允许管理员或者博客的作者访问
    """
    # 方法名不可变:has_permission(), 有权限访问
    def has_permission(self, request, view):
        if request.user.is_staff:
            return True
        return False

    # 方法名不可变:has_object_permission(), 有权限访问指定对象
    def has_object_permission(self, request, view, obj):
        return obj.author == request.user # 举例:只有当该对象的作者和登录的用户是一样时,才可以被允许访问
```

- 通过`has_permission`防爬虫

```py
...
    def has_permission(self, request, view):
        if request.META.get('HTTP_REFERER') #判断是否有历史记录
            return True
        else:
            return False #否则不允许访问
...
```

- 通过上述逻辑, 爬虫想要直接获取页面信息时, 因为它没有历史记录(不是人一步一步点进来的), 所有没有权限访问.
- 具体使用

```py
# 影响全局, 写在 settings.py 中
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAdminOrAuthor', #项目中所有的视图函数都必须有该权限才可访问
    ]
}

# 只针对某一视图, 写在 views.py 中
class ExampleView(APIView):
    permission_classes = [IsAdminOrAuthor]
    # ...
```

# 限速节流

- 一般写在 settings.py 中,有三种限速节流:
  1. 针对未登录用户的:`AnonRateThrottle`
  2. 针对已登录用户的:`UserRateThrottle`
  3. 针对某一视图的特殊规则:`ScopedRateThrottle`
- 具体实现 1 和 2

```py
# settings.py 中给REST_FRAMEWORK 添加以下属性
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle', # 针对未登录用户
        'rest_framework.throttling.UserRateThrottle' # 已登录用户
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day', # 未登录用户100次每天
        'user': '1000/day' # 已登录用户1000次每天
    }
}

# views.py 中给需要被限制访问的视图API声明throttle_classes
from rest_framework.response import Response
from rest_framework.throttling import UserRateThrottle
from rest_framework.views import APIView

class ExampleView(APIView):
    throttle_classes = [UserRateThrottle]

    # ...
```

- 具体实现第 3 种方式, 给指定视图配置特殊规则

```py
# views.py 在视图中声明 throttle_scope 的值
class ExampleView(APIView):
    throttle_scope = 'example'
    # ...

# setttings.py 中
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.ScopedRateThrottle', #先配置它
    ],
    'DEFAULT_THROTTLE_RATES': {
        'example': '10/d' # ExampleView 每天只能被请求10次
    }
}
```

- 关于请求次数的单位: `次数/d天h时m分s秒`

# 分页

- 全局分页: 所有 API 返回的列表类型的数据,都遵循此规则,写在`settings.py`中

```py
REST_FRAMEWORK = {
    # 设置默认分页类
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    # 声明每页10条数据
    'PAGE_SIZE': 10
}
```

- 视图分页: 为指定视图集设置具体分页规则

```py
# paginations.py
from rest_framework.pagination import PageNumberPagination

# Merchant的分页规则
class MerchantPagination(PageNumberPagination):
    # 默认情况下一页展示多少条数据
    page_size = 5
    # 一页，最大不能超过多少条数据
    max_page_size = 10
    # 用户自己指定一页展示多少条数据的参数名称吗, 默认是size
    page_size_query_param = 's'
    # 用户指定页码的参数, 默认是page
    page_query_param = 'p'


# views.py
# 导入分页器
from .paginations import MerchantPagination

# 配置视图集
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer
    pagination_class = MerchantPagination # 为视图集设置分页器

    # ...
```

- 既在 `settings.py` 中配置全局分页规则, 又在某视图中给指定了特定分页器, 谁生效?
  > **局部优先级大于全局**

# 异常处理

### drf 自带的异常

- APIException：所有异常的父类。
- ParseError：解析错误(没传入必须的参数)
- AuthenticationFailed：认证失败(用户名密码不正确)
- NotAuthenticated：未认证(未登录)
- PermissionDenied：没有权限访问(用户权限)
- NotFound：路由没找到(没有指定路由)
- MethodNotAllowed：请求方法不允许(只允许 GET 请求的路由用了 POST)
- NotAcceptable：要获取的数据格式不支持
- Throttled：超过限流次数。
- ValidationError：表单验证失败。

### 自定义异常

> 如果遇到其他异常，DRF 将无法处理，这时候就会导致程序抛出错误。为了处理其他错误，我们可以自定义异常处理函数.

- 新建 `exceptions.py` 并配置规则

```py
# 导包
from rest_framework.views import exception_handler # 异常处理器
from rest_framework.response import Response # drf响应类
from rest_framework import status # drf 状态码
from django.db import DatabaseError # django 数据库异常类


def my_exception_handler(exc, context):
    # print(type(exc)) # 异常类
    # print(context) # 报错上下文

    response = exception_handler(exc, context) # 先实例化一个response对象

    # 在response在任意位置被return前, 默认是都是None, 所以发生异常后终止代码,不会return 一个有值的 response
    # 也就意味着, response为空时, 发生了异常
    if response is None:
        # isinstance(对象, 类) : 判断该对象是否为该类的一个实例
        if isinstance(exc, DatabaseError):
            # 如果是, 那么就是发生了数据库异常
            response = Response({'detail': '数据库操作异常！'}, status=status.HTTP_500_INTERNAL_SERVER_ERROR)
        else:
            # 如果不是, 那么就是其他异常
            response = Response({'detail': '出现异常了！'}, status=status.HTTP_500_INTERNAL_SERVER_ERROR)

    # 如果没进if, 说明 response  有值,一切正常,直接返回
    return response
```

- 一定要在 `settings.py` 中导入

```py
REST_FRAMEWORK = {
    # 我的异常处理器是自己写的,写在 ~/myaap/exceptions.py/my_exception_handler() 中
    'EXCEPTION_HANDLER': 'myapp.exceptions.my_exception_handler'
}
```

# 总结 DRF

### 投入使用

1. 安装: `pip install djangorestframework`
2. 在`settings.py`中注册

```py
INSTALLED_APPS = [
    # ...
    'rest_framework',
    # ...
]
```

### 序列化 serializers.py

> 所谓序列化, 主要功能是将 _ORM_ 转为 _JSON_, 兼带 _表单数据验证_ 功能

- 一个标准的`serializer.py`文件

```py
from rest_framework import serializers # 导入父类
from meituan.models import Merchant # 导入要序列化的模型

# 命名规则 模型名+Serializer
class MerchantSerializer(serializers.ModelSerializer):
    # 配置Meta
    class Meta:
        # 指定模型
        model = Merchant
        # 指定要序列化的字段, all 表示全部
        fields = "__all__"
        # 如果有不想展示的字段:
        # exclude = ['name']
```

- 序列化嵌套: 模型关系的映射, 1 对多? 多对多? 多对 1?

```py
class GoodsSerializer(serializers.ModelSerializer):
    class Meta:
        model = Goods
        fields = "__all__"

class MerchantSerializer(serializers.ModelSerializer):
    class Meta:
        model = Merchant
        fields = "__all__"

class GoodsCategorySerializer(serializers.ModelSerializer):
    merchant = MerchantSerializer(read_only=True,required=False)
    goods_list = GoodsSerializer(many=True,required=False)
    merchant_id = serializers.IntegerField(required=True,write_only=True)
    class Meta:
        model = GoodsCategory
        fields = "__all__"

    def validate_merchant_id(self,value):
        if not Merchant.objects.filter(pk=value).exists():
            raise serializers.ValidationError("商家不存在！")
        return value

    def create(self, validated_data):
        merchant_id = validated_data.get('merchant_id')
        merchant = Merchant.objects.get(pk=merchant_id)
        category = GoodsCategory.objects.create(name=validated_data.get('name'), merchant=merchant)
        return category
```

- 上面的代码中:
  - 有"GoodsSerializer", *商品*模型序列化
  - 有"MerchantSerializer", *商家*模型序列化
  - 有"GoodsCategorySerializer", *商品分类*模型序列化, 与商品形成(1 分类:n 商品)的关系,与商家形成(n 分类:1 商家的关系)
  - 所以当我们想获得分类下所有的商品时:`goods_list = GoodsSerializer(many=True,required=False)` 1:n
  - 然而当我们想获得分类所属的商家时:`merchant_id = serializers.IntegerField(required=True,write_only=True)`
    - 同时我们还需要重写 create 方法, 因为我们在创建一个分类时, 不可能把商家信息写全, 只会写一个外键:商家 id 进去
    - 所以第一步,`validate_merchant_id(self,value)`验证商家是否存在
    - 第二步,`create(self, validated_data)`重写 create 方法
  - 关于 read_only, write_only(只读, 只写):
    - `read_only=True`：这个字段只只能读，只有在返回数据的时候会使用。
    - `write_only=True`：这个字段只能被写，只有在新增数据或者更新数据的时候会用到。

### Request 请求, Response 响应

- 在请求方面, DRF 与 Django 最大的区别就是, DRF 拥有更好的代码可读性, 比如 POST 提交的表单数据
  - 在 Django 中,我们需要`data = request.POST`
  - 在 DRF 中,我们更直白`data = request.data`
- 在响应方面, DRF 有一个自己的 Response 类, 和配置好的状态码

```py
# 在pycharm编辑器中, ctrl+鼠标左键点击导入的东西,就可以溯源的源代码
from rest_framework.response import Response # DRF.Response类
from rest_framework import status # DRF.状态码

# 假设现在有个视图函数
def merchant(request):
    # 在我们访问这个视图函数时, DRF.Response类会帮我们返回以下数据:参数1一个json字符串,参数2一个http状态码
    return Response({"message":"哈哈哈"}, status=status.HTTP_200_O)
```

### 视图集

- 模型视图集: 一个强大的,自带增删改查(`create新增`,` destory删除`, `update更新`, `list列表`&`retrieve详情`)的视图类
- 定义一个模型视图集合 `views.py`

```py
from rest_framework import viewsets # 导入父类
from meituan.models import Merchant # 导入模型
from .serializers import MerchantSerializer # 导入序列化

# 命名规则 模型名 + ViewSet
class MerchantViewSet(viewsets.ModelViewSet): # 继承父类
    queryset = Merchant.objects.all() # 配置查询语句
    serializer_class = MerchantSerializer # 配置序列化

    # 好了,增删改查等一系列常见操作,都在父类中实现了,而这里,只需要这一点代码用于配置的
```

- 当然这个视图集还需要配置路由 `urls.py`

```py
from rest_framework.routers import DefaultRouter # 导入 DRF.默认路由
from .views import MerchantViewset # 导入 视图集

app_name = 'quickstart' # app名称

router = DefaultRouter() # 实例化一个router
router.register('merchant',MerchantViewset,basename='merchant') # 把视图集注册到路由中

urlpatterns = [] + router.urls # 再拼接到urlpatterns中

# 最后在主路由里面 include() 进去即可,略
```

- 当然, 一个 API 有可能不止"增删改查"四个操作, 比如条件查询?
- 那么这时候就需要导入装饰器`@action`并且在 ModelViewSet 里再写上被装饰的自定义函数,举例:

```py
# ...
from rest_framework.decorators import action # 导入 DRF.装饰器.action
# ...

# 假如在商家视图集合中:
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer

    # 我们还有一个自定义函数, 用于获取商家名称中, 带"四川"两个字的商家
    @action(['GET'], detail=False, url_path='sc') # @action(['要求用GET方法访问'], 并不是获取单一数据而是列表, url_path="sc") 意味着访问该方法的路由是`../sc`而非方法名,不写就默认是方法名
    def sichuan(self, request):
        # 查询数据库,转为ORM模型
        queryset = self.get_queryset()
        result = queryset.filter(name__contains='四川')
        # 序列化模型,转为JSON
        serializer_class = self.get_serializer_class()
        serializer = serializer_class(result, many=True)
        # 把结果返回给前端
        return Response(serializer.data)
```

### 认证和权限

- 认证方面: 目前而言, 最被广泛采用的认证方法是`PyJWT`, 其原理可以理解为
  1. 用户登录成功后, 服务器把用户的相关信息+过期时间进行加密, 并把加密后的数据作为一个 token(认证令牌)返回给客户端
  2. 客户端存储着这个 token
  3. 以后每次请求的时候都携带这个 token(在请求头里), 服务器在接收到需要登录的 API 请求后，对这个 token 进行解密，然后获取过期时间和用户信息,如果过期了或者用户信息不对,那么都是认证失败
  - 具体实现在实际项目中有更直观的例子, 此处略
- 权限方面: DRF 自带 4 种权限,分别是`未登录`, `已登录`, `是否是admin`, `是否是只读`,同时我们可以在某个 app 中自定义规则
- 新建 `peimissions.py`

```py
from rest_framework import permissions # 导入父类
# 配置规则
class MyPermission(permissions.BasePermission):
    # 重写 has_permission 访问所有方法时的权限规则
    def has_permission(self, request, view):
        # 这里实现了一个简单的放爬虫:
        if request.META.get('HTTP_REFERER'): # 当有历史记录时
            return True # 可以访问
        return False # 如果是直接访问(爬虫爬取数据不会像人一样一下一下点进来), 那么不允许访问

    # 重写 has_object_permissio 访问具体
    def has_object_permission(self, request, view, obj): # 这里多一个第4参数 obj
        if '四川' in obj.name: # 假如我访问的这个对象的名字里带"四川"二字
            return True # 那么允许访问
        else: # 不然
            return False # 不允许访问
```

- 具体使用举例, 在某个 `views.py` 中

```py
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer

    # authentication_classes = [认证规则1, 认证规则2]
    # permission_classes= [权限规则1, 权限规则2]
```

### 限速节流

> 限速节流: 限制一定时间内的访问次数

- DRF 首先自带三个规则

  1. `AnonRateThrottle`, 针对没有登录者的规则
  2. `UserRateThrottle`, 针对已经登录者的规则
  3. `ScopedRateThrottle`, 特定规则, 不管你登录没登录

- 实现第 1 规则和第 2 规则,我们可以直接在`settings.py`中

```py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle', # 未登录的
        'rest_framework.throttling.UserRateThrottle' # 已登录的
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day', # 未登录的1天可以向服务器发送100次请求
        'user': '1000/day' # 已登录的1天可以向服务器发送1000次
    }
}
```

- 然后在视图中 `views.py`

```py
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer

    # authentication_classes = [认证规则1, 认证规则2]
    # permission_classes= [权限规则1, 权限规则2]
    # throttle_classes = [AnonRateThrottle, UserRateThrottle]

    # 现在访问这个API, 未登录1天只能请求100次, 已登录1天只能请求1000次
```

- 实现第 3 规则, 就和 1,2 冲突, `settings.py`里需要这样写:

```py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.ScopedRateThrottle', # 使用ScopedRateThrottle规则
    ],
    'DEFAULT_THROTTLE_RATES': {
        'hahaha': '1000/day', #hahaha 是什么?在哪里?
    }
}
```

- 原来在视图层中`views.py`

```py
class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer

    # authentication_classes = [认证规则1, 认证规则2]
    # permission_classes= [权限规则1, 权限规则2]

    throttle_scope = 'hahaha' # 告诉 DRF, 我这个 API 的节流限速规则名称叫hahaha
```

- 节流限速的单位包括
  - 天 `d/day`
  - 时 `h/hour`
  - 分 `m/minute`
  - 秒 `s/second`
  - 写法:`多少次/每天or时or分or秒`, `10/m` => 每分钟 10 次

### 分页

> 访问所有 ViewSet.list() 时,我们通过 http 不同的 params.page 参数, 获取当前页, 再通过页数获取指定数据

- DRF 有:
  1. 全局默认分页
  2. 自定义分页
- 全局默认分页写在 `settings.py` 中

```py
REST_FRAMEWORK = {
    # 这一句不要变,声明分页规则是 PageNumberPagination
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    # 默认每页 10 条数据
    'PAGE_SIZE': 10
}
```

- 自定义分页写在某个需要使用自定义规则的 app 下的 `paginations.py` 中, 举例

```py
from rest_framework.pagination import PageNumberPagination # 导入父类

class MerchantPagination(PageNumberPagination): # 继承后重写属性
    # 默认情况下一页展示多少条数据
    page_size = 5
    # 一页，最大不能超过多少条数据
    max_page_size = 10
    # 用户自己指定一页展示多少条数据的参数名称
    page_size_query_param = 's'
    # 用户指定页码的参数，默认是page
    page_query_param = 'p'
```

- 要想使用, 在 `views.py` 中:

```py
# ...
from .paginations import MerchantPagination #导入分页器
# ...

class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer

    # authentication_classes = [认证规则1, 认证规则2]
    # permission_classes= [权限规则1, 权限规则2]
    # throttle_classes = [AnonRateThrottle, UserRateThrottle]


    # 如果视图中使用了自己的分页器，那么会覆盖掉全局的分页配置，优先级：局部>全局
    pagination_class = MerchantPagination

    # 现在,通过访问 `../某个模型视图集路由/?p=2&s=9`时, 也就是访问的展示9条数据的第2页的list
```

### 再回头看看一个 ViewSet

- `views.py`

```py
# 省略导包

class MerchantViewSet(viewsets.ModelViewSet):
    queryset = Merchant.objects.all()
    serializer_class = MerchantSerializer # 这时候一个自带增删改查功能的api已经实现

    authentication_classes = [认证规则1, 认证规则2] # 这时候认证规则已经实现
    permission_classes= [权限规则1, 权限规则2] # 这时候访问权限已经实现
    throttle_classes = [AnonRateThrottle, UserRateThrottle] # 这时候限速节流已经实现

    pagination_class = 指定的分页器类 # 这时候列表分页功能也已实现
```

- 发现视图层(api)非常简洁
- 我们只需要分别实现`authentications.py`, `permissions.py`, `paginations.py` 即可

### 异常处理
- DRF 内置的异常处理已经足够完善, 但我们有时候仍需要处理一些DRF处理不了的异常? 新建`exceptions.py`, 写好异常处理逻辑
```py
from rest_framework.views import exception_handler
from rest_framework.response import Response
from rest_framework import status
from django.db import DatabaseError

def merchant_exception_handler(exc, context):
    # 先调用DRF异常处理方法，获取响应对象
    response = exception_handler(exc, context)

    # 如果response是None，则出现了DRF无法处理的异常
    if response is None:
        if isinstance(exc, DatabaseError): #isinstance(obj, class), 判断对象是不是类的实例
            view = context['view']
            print(f'[{view}]: {exc}')
            response = Response({'detail': '数据库操作错误！'}, status=status.HTTP_500_INTERNAL_SERVER_ERROR)

    return response
```
- 上面的方法实现了数据库出现异常时的异常处理逻辑, 要想生效, 还需要在`settings.py` 中
```py
REST_FRAMEWORK = {
    'EXCEPTION_HANDLER': '[exceptions.py所在的app路径].exceptions.[异常处理方法merchant_exception_handler]'
}
```
