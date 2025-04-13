# fastAPI

## 简介

- 和 django 一样, 是一个 python.web 开发框架
- 和 django 相比, fastAPI 性能更好, django 开发更快
- 著名 python.web 框架中, fastAPI 最"小", flask 其次, django 最大(封装的功能最多), 但性能方面相对相反(当然django性能也不差)

## 安装

- 命令: `pip install "fastapi[standard]"`, 建议添加参数 `[standard]` (安装稳定版)

## 第一个 fastAPI 程序

> 本课程主要采用异步版本

### 搭建环境

- 新建项目 `fastAPI_demo`
- vsCode搞虚拟环境那一套
- 安装依赖

### 编写代码

- `fastAPI_demo/main.py`

```python
# 导入 fastAPI
from fastAPI import FastAPI
# 导入 django 官方的类型定义功能
from typing import Optional

# 实例化 fastAPI
app = FastAPI()

# 以装饰器的形式定义路由
@app.get('/')
async def index():
    return {'message': '首页'}

# 定义路由参数: item_id 为必传参数 url/必传
@app.get('item/{item_id}')

# 定义路由参数, q 为查询参数 url?key=value
async def item(item_id: int, q: Optional[str]=None) -> dict: # -> dict : 函数必须返回 dict, 有点ts的感觉
    async def item(item_id: int, q: Optional[str]=None) -> dict:
    if q:
        message = f"必传的参数item_id是{item_id}, 可选的参数q是{q}"
    else:
        message = f"必传的参数item_id是{item_id}"

    return {'message': message}
```

### 运行项目

- 命令: `fastapi dev main.py`
- 上面的命令是 fastAPI 最近官方提供的开发环境下运行的命令, 每次修改代码并保存后, 项目都会重新加载代码再启动
- 但如果实际投入生产环境, 我们必须安装 Uvicorn: `pip install "uvicorn[standard]"`, 然后使用命令 `uvicorn main:app --reload` (--reload表示修改代码并保存时重载)
- 无论用那种方法启动, 都可以指定端口号, 命令末尾添加参数 `--port 你希望项目运行在哪个端口`

> 建议使用 Uvicorn, fastapi dev main.py 这条命令本质上也是封装的这条命令

## python 类型检查

- js 有 typescript ,python 也有自己的 "typepython"
- vsCode想实现静态类型检查和写python的极佳环境, 需要这几个扩展:`Mypy Type Checker`, `Pylance`, `Python`,`PythonDebugger`(都是微软官方出品的插件)
- 定义变量: `变量名: 数据类型 = 赋值`
- 定义函数: `def func(参数名: 参数类型, ...) -> 函数返回值类型: ...`

### 基本类型 demo

```python
# 定义变量时限制类型
username: str = '张三'
age: int = 18

# username = 1 # 静态类型检查会报告此错误

# 定义函数时限制参数类型
def add(a: int, b:int) -> int:
    return a+b

"""
常用的基本数据类型:
    int 
    float
    str
    bool
    None
    ...
"""
```

### 第三方类型检查库

- 安装命令: `pip install mypy`
- 执行检查: `mypy 要检查的文件.py, 还可以添加其他文件`
- 检查整个项目: `mypy yourProjectFloders/`

### 容器类型

- 示例代码

```python
from typing import List, Dict, Tuple, Set, Union, Optional, Any


# list
userniversity: List[str] = [
    '清华',
    '北大',
    '家里蹲',
    # 123 # 静态类型检查报错: 必须是 str 
]

# 这里其实已经用了一次不写 Union 关键字的联合类型写法了
example_list: List[str | int] = [
    '字符串可以',
    123, # 整形也可以
]

# Dict
scores: Dict[str, int] = {
    '数学': 5,
    '英语': 90,
}

# Union 
example_dict: dict[str, Union[int, str]] = {
    # 新版python可以直接将上面的 "Union[int, str]" 替换为 "int | str"
    "age": 25,       # value 为联合类型, 既可以是int
    "name": "Tom"    # 也可以是str
}

# Tuple
server: Tuple[str, int] = ('127.0.0.1', 8000)

# Set
keys: Set[str] = {'1', '2'}

# Optional, 要么是指定类型, 要么是None
x: Optional[str] = None
# x: str | None # 两者等价

# Any 不写类型检查, 默认就是 Any
a: Any = 'xxx'

# 自定义类型
type StrList = List[str]

def get_urls(list: StrList):
    pass
```

## Pydantic

- 类似于 django.forms / serializers, 且有更强的类型提示提供支持, 底层采用Rust, 是最快的数据验证库之一

- 安装命令: `pip install pydantic` (安装 fastAPI时, 默认已经安装了)

- 基本使用

```python
# 导包
from pydantic import BaseModel, PositiveInt, ValidationError
from datetime import date


# 必须要继承 BaseModel
class User(BaseModel):
    # 开始配置模型字段以及每个字段的数据类型
    id: int
    # key:type = 默认值
    name: str = '张三'
    # date 也是一种类型, 来自datetime
    date_joined: date | None
    # 口味字段, 是字典类型, key 是 字符串, value 是 正整数 PositiveInt
    tastes: dict[str, PositiveInt]

if __name__ == '__main__':
    try:
        user = User(
            id = 123,
            name = '李四',
            # 加入时间这里静态类型检查会报错 ,但是如果不开启pydantic严格模式, 还是可以传过去的, 他会自动将字符串转为date类型
            date_joined = '2023-03-03',
            tastes = {
                '川味': 10,
                '广味': 20
            }
        )

        print (user.id, user.date_joined)
    except ValidationError as e:
        print(e.errors())
```

- 一定要继承 `BaseModel` 类
- 出现错误都在 `ValidationError` 里
- 正整数 `PositiveInt`

> 在学习这里的时候犯了个特别二的错误: 我稀里糊涂地把文件名字写为 pydantic.py 了, 然后发现导包导不进来, 我搁着自己导自己呢! **命名文件时切忌和已经存在的包重名!**

## 请求数据

### 路由参数

- demo

```python
from fastapi import FastAPI, Path, Query


app = FastAPI()

@app.get('/')
async def root():
    return {"message": "根"}

# 路径参数: 作为路由的一部分, 必传, 否则404
@app.get("/hello/{name}")
# 参数 (参数名name: 参数类型str = Path(max_length=最长10位, 描述="生成api文档时展示的描述类似注释"))
# 想要查看api文档, 启动服务器后, 访问 127.0.0.1:8000/docs 即可
async def say_hello(name: str = Path(max_length=10, description='路径参数name: 必须填写用户名')):
    """
    这里的内容也会在 /docs 路由下展示
    :param name:
    :return:
    """
    # 路径参数, 可以直接在函数内部读取
    return {'message': f"你好, {name}"}

# 查询参数: ?key=value&key=value
@app.get("/books")
# 参数 (..., page_size: int = 默认值为10)
# async def book_list(page: int, page_size: int = 10):
async def book_list(
    page: int, page_size: int = Query(default=10, description="查询参数page_size:每页数量, 默认为10")
):
    # 查询参数, 同样可以在函数内部直接读取, 但是如果不传入且没有默认值, 会报错
    return {"message": f"page是{page}, page_size是{page_size}"}

```

- 路径参数: ...url/xxx
- 路径参数可以使用 Path 包装: `参数名: 参数类型 = Path(一些限限制, description="描述信息")`
- 查询参数: ...url?key=value
- 查询参数可以使用 Query 包装: `参数名: 参数类型 = Query(默认值, 一些限制, description="...")`
- 这些参数都可以在函数内部直接访问
- 描述信息, 文档注释都可以在 `当前服务器默认127.0.0.1:8000/docs` 页面上查看

### Body 上的参数

- 示例代码

```python
from fastapi import FastAPI, Path, Query, Body
from pydantic import BaseModel, field_validator, Field
from typing import Annotated


class Item(BaseModel):
    """
    定义模型
    """
    # 配置字段 数据名称: 数据类型 = Field(一些基础验证, 可以配置默认值)
    name: str = Field(max_length=10, default='无名书籍')
    # 这里可以实现稍微详细的验证规则 将Field作为元数据metadata传入
    description: Annotated[float, Field(max_length=10)]
    price: float | None = 0
    
    # 类方法实现更细致的字段验证方法
    @field_validator('name')
    @classmethod
    def validate_name(cls, value: str):
        if ' ' in value:
            raise ValueError('名称中不能包含空格')
        
        return value

@app.put("/item/{item_id}")
async def update_item(item_id: int, item: Item = Body(description="传入编辑的数据")):
    print(f"item_id是{item_id}")
    print(f"item的name是{item.name}")

    return {"message": f"修改数据已传入, 在服务器控制台查看"}
```

- Body 数据通过PostMan配置请求的Body.raw传入(此处学习暂用json格式)
- 验证 Body 数据, 需要定义一个 pydandic Basemodel
- 基本的数据类型验证直接在类属性上配置类型, 也可以 `=Field()` 进一步配置
- 实现稍复杂的数据类型可以通过 Anootated 类型, 将 Field 作为元数据实现验证
- 更详细的验证方法就可以通过定义类方法(两个装饰器 `field_validator` 加工, `classmetod` 声明为类方法)实现

### Cookie 上的参数

- 示例代码

```python
from fastapi import Cookie
from fastapi.responses import JSONResponse


@app.get('/cookie/set')
async def set_cookie(
    # 配置接收 cookie
    username: str | None = Cookie(description='cookie数据_username', default=None),
    password: str | None = Cookie(description='cookie数据_password', default=None)
):
    # print (f"username: {username}, password: {password}")

    # 初始化响应对象
    response = JSONResponse(content={"message": "cookie设置成功"})

    # 配置cookie
    response.set_cookie('username', username)

    # 返回响应
    return response
```

- 同样在参数中接收
- 发送 Cookie 需要 PostMan 配置请求 header `Cookie`, `key=value没有引号没有空格;如果有第二个参数用分号隔开`
- 设置 Cookie 需要借助 JSONResponse, 先实例化一个对象, 再调用 `对象.set_cookie()` 函数
- 最后我们在客户端会得到`{"message": "cookie设置成功"}`, 同时 cookie 里(PostMan 右上角)会显示客户端给设置的 Cookie

### 请求头上的参数

- 请求头上有很多隐藏参数, 打开 PostMan, 创建任意请求, 配置 Headers 时, 这些东西默认隐藏 Hidden
- 开启后就会看见, 有比如 User-Agent 这些属性, 但以 User-Agent 为例, 这个名称是有`-`符号的, 并不能在服务端获取, 所以它在服务端叫做 `user_agent`
- 示例代码: 获取请求你头里的数据

```python
from fastapi import Header


@app.get('/header/get')
# 定义方式同样
async def get_header(user_agent: str | None = Header(description='请求头里的 User-Agent')):
    print(f"客户端传来的User-Agent是: {user_agent}")

    return {"message": "请求头参数接收成功"}
```

- 传过来的命名规则: 大写变小写, `-`变`_`

### 总结

1. 所有客户端传来的数据, 都可以通过参数的形式接收, 有 `Path, Query, Body, Cookie, Header` 等
2. 想要接收不同请求不同形式的参数, 只需要定义接口函数时, 在参数中声明 `(变量名: 变量类型 = 变量位置或者形式(其他参数))`
3. 只要成功接收, 那么函数里可以直接通过变量名对这些参数进行读取和操作

## 依赖注入

> 将重复的代码抽成“依赖”, 在需要使用的地方注入依赖

- 函数依赖项, 类依赖项

```python
from fastapi import FastAPI, Depends
from typing import Dict
from fastapi.responses import JSONResponse


app = FastAPI()

@app.get('/')
async def root():
    return {"message": "根"}

# 函数依赖项, 哲理假设该函数依赖项将在未来处理公共的列表分页和查询
async def list_common(
    page: int = 1,
    size: int = 10,
    q: str | None = None
):
    query_params = []
    if q:
        query_params = q.split('+')

    return {
        "page": page,
        "size": size,
        "query_params": query_params
    }

@app.get("/items")
# 注入依赖, 避免重读定义相同的参数, 比如 items 列表, 和 users 列表都需要 page, size, 查询字符串等等
async def get_items(common: Dict = Depends(list_common)):
    message = f"传入的页码是{common['page']}, "
    message += f"传入的每页数量是{common['size']}, "
    message += f"传入的查询字符串有: "

    for index, query_param in enumerate(common['query_params']):
        message += query_param

    return {"message": message}

@app.get("/users")
async def get_users(common: Dict = Depends(list_common), q: str | None = None):
    message = f"传入的页码是{common['page']}, "
    message += f"传入的每页数量是{common['size']}, "
    message += f"传入的查询字符串有: "

    for index, query_param in enumerate(common['query_params']):
        message += query_param

    # 可以自己接收重名的参数, 但值和 common 里的一样
    message += f"自己接收的参数是{q}"

    return {"message": message}

# 类依赖项
class CommonQueryParam:
    def __init__(self, q:str | None = None, skip: int = 0, limit: int = 100):
        self.q = q
        self.skip = skip
        self.limit = limit

    def get_query_params(self) -> list:
        query_params = []
        if self.q:
            query_params = self.q.split(" ")

        return query_params

@app.get('/books')
async def get_books(params = Depends(CommonQueryParam)):
    return {"message": f"用户传入的query_params为{params.get_query_params()}"}
```

- 子依赖项: 一个依赖可以注入其他的依赖项, 可以多层依赖, 略

- 视图装饰器依赖: `@app.get("/items", dependencies = [指定没有返回值的依赖项])`, 略

- 全局依赖(类似中间件), 在初始化app时, `app = fastAPI(dependencies = [依赖项])`

- 模块依赖, 配置路由时使用, 下面路由有示例

- 总结一句话, 通过 `Depend` 注入依赖

## APIRouter

> 我们不可能将所有视图代码都放在 `main.py` 中, 这时候就需要将视图进行分类, 通过 `APIRouter` 实现

- 新建 `~/router/user.py` 以及 `~/router/items.py`, 以 `items.py` 为例:

```python
from fastapi import APIRouter, Header, Depends
from fastapi.exceptions import HTTPException


# 定义依赖项
async def login_required(
    token: str = Header()
):  
    # 注入路由器后, 访问此路由器下的所有路由, 必须传入 Header.token 且必须是 'liudehua' (模拟登陆认证)
    if token != 'liudehua':
        raise HTTPException(status_code=403, detail='认证令牌验证失败')

# 定义路由
router = APIRouter(
    # 路由前缀
    prefix = '/item',
    tags = ['item'],
    # 注入依赖, 现在必须“登录”才能访问
    dependencies = [Depends(login_required)]
)

# 装饰器的形式注册路由, 现在必须通过 url/item/list才能访问
@router.get('/list')
async def get_item_list():
    return {"message": "items列表"}
```

- 必须要在 `~/main.py` 中注册路由:

```python
from fastapi import FastAPI
from routers import users, items


app = FastAPI()

# 注册路由
app.include_router(users.router)
app.include_router(items.router)
```

## 操作数据库

### 准备工作

- 创建一个用于学习的数据表 `fastapi_sqlalchemy`
- 通过 **SQLAlchemy** 操作数据库, 安装异步的 sqlalchemy: `pip install "sqlalchemy[asyncio]"`
- 还需要一个异步驱动 `pip install asyncmy`, 但要使用该工具, 需要依赖Cython编译扩展模块，而Windows系统需要Visual C++ Build Tools支持, 以 windows 系统为例:
    1. 访问 <https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/> 下载Microsoft C++ Build Tools, 然后双击运行
    2. 搜索并勾选合适的 "C++ 生成工具", "Windows 10 SDK", 建议直接选择 `使用 C++ 的桌面开发`
- 使用Python连接MySQL需要用cryptography对密码进行加密，所以还需要安装以下包: `pip install cryptography`

### SQLAlchemy 连接数据库

- 新建 `~/models/__init__.py`

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base


# 配置连接引擎
engine = create_async_engine(
    # 数据库URI配置: "数据库+驱动://用户名:密码@主机:端口/数据库名称?charset=utf8mb4"
    "mysql+asyncmy://root:root@127.0.0.1:3306/fastapi_sqlalchemy?charset=utf8mb4",
    # 将输出所有执行SQL的日志（默认是关闭的）
    echo=True,
    # 连接池大小（默认是5个）
    pool_size=10,
    # 允许连接池最大的连接数（默认是10个）
    max_overflow=20,
    # 获得连接超时时间（默认是30s）
    pool_timeout=10,
    # 连接回收时间（默认是-1，代表永不回收）
    pool_recycle=3600,
    # 连接前是否预检查（默认为False）
    pool_pre_ping=True,
)

# 创建会话工厂
AsyncSessionFactory = sessionmaker(
    # Engine或者其子类对象（这里是AsyncEngine）
    bind=engine,
    # Session类的代替（默认是Session类）
    class_=AsyncSession,
    # 是否在查找之前执行flush操作（默认是True）
    autoflush=True,
    # 是否在执行commit操作后Session就过期（默认是True）
    expire_on_commit=False
)

# 创建模型
Base = declarative_base()
```

- 可以将create_async_engine的第一个参数配置为常量再投入使用, 比如: `DB_URI = "数据库软件+驱动程序://用户名:密码@主机名:端口号/要连接的数据库名称?charset=utf8mb4"`

- 创建会话工厂部分会报静态检查错误, 不用管他

- 上面的代码可以复用

### 创建模型

- 新建用户模型 `~/models/users.py`

```python
# 这里的Base就是__init__.py里的Base
from models import Base
from sqlalchemy import Column, Integer, String


class User(Base):
    __tablename__ = 'user'
    id = Column(Integer, primary_key=True, autoincrement=True, index=True)
    email = Column(String(100), unique=True, index=True)
    username = Column(String(100), unique=True)
    password = Column(String(200), nullable=False)
```

- 为了方便外部使用该模型, 可以在`__init__.py`里导入 User

```python
# 之前的代码...

# 导入其他模型的python文件
from . import users
```

### 执行迁移

- 安装迁移管理工具: `pip install alembic`
- 安装完成后, 创建迁移仓库 `alembic init alembic --template async`, 这会生成一个 `~/alembic/` 文件夹, 以及 `~/alembic.ini` 配置文件, 编辑:
  - `alembic.ini`: `sqlalchemy.url = 之前写的数据库URI配置`
  - `~/alembic/env.py`:

  ```python
    # 找到 target_metadata = target_metadata 这句话, 修改为:
    from models import Base
    target_metadata = Base.metadata
  ```

- 创建迁移 `alembic revision --autogenerate -m "修改的内容"`, 这会自动在`~/alembic/versions/` 下面生成一个python文件(迁移命令就在里面)
- 执行迁移, 创建数据表 `alembic upgrade head`
- 如果需要回退, 执行命令 `alembic downgrade`

### 多表关系

- 1 对 1 关系,  `user.py`

```python
from models import Base
from sqlalchemy import Column, Integer, String, ForeignKey
from sqlalchemy.orm import relationship


class User(Base):
    """用户模型"""
    __tablename__ = 'user'
    id = Column(Integer, primary_key=True, autoincrement=True, index=True)
    email = Column(String(100), unique=True, index=True)
    username = Column(String(100), unique=True)
    password = Column(String(200), nullable=False)

class UserExtension(Base):
    """用户扩展信息 1:1 用户"""
    __tablename__ = 'user_extension'
    id = Column(Integer, primary_key=True, index=True)
    university = Column(String(100))
    # 1:1 外键
    user_id = Column(Integer, ForeignKey("user.id"))
    # 通过 relationship 函数指定关联模型, 通过 backref="指定名称"后, 可以通过 user.extension 即可获取用户扩展信息, uselist参数意为该信息是否是多条, 一个用户只有一个扩展信息, 所以为 False
    user = relationship(User, backref="extension", uselist=False)
```

- 1 对 多关系, 多 对 多关系 `article.py`

```python
from models import Base
from .user import User
from sqlalchemy import Column, Integer, String, ForeignKey, Text
from sqlalchemy.orm import relationship


class Tag(Base):
    """文章 n:n 标签"""
    __tablename__ = 'tag'
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(100), unique=True)
    # 同理, 通过 relationship 函数, secondary指定第三方表, back_populates指定索引名称, lazy="dynamic" 声明懒加载
    articles = relationship("Article", secondary="article_tag", back_populates='tags', lazy="dynamic")

class Article(Base):
    """文章 n:1 用户"""
    __tablename__ = 'article'
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(100), nullable=False)
    content = Column(Text, nullable=False)
    author_id = Column(Integer, ForeignKey("user.id"))
    author = relationship(User, backref="articles")
    tags = relationship(Tag, secondary="article_tag", back_populates="articles", lazy="dynamic")

class ArticleTag(Base):
    """通过第三数据表实现多对多关系绑定"""
    __tablename__ = 'article_tag'
    id = Column(Integer, primary_key=True, index=True)
    article_id = Column(Integer, ForeignKey("article.id"))
    tag_id = Column(Integer, ForeignKey("tag.id"))
```

- 导入 `relationship` 函数: `from sqlalchemy.orm import relationship`
- 1:1 关系, 通过 `user_id = Column(Integer, ForeignKey("user.id"))` 指定外键, 通过relationshi函数指定映射名称 `user = relationship(User, backref="extension", uselist=False)`
- 1:n 关系, 指定外建后, 声明关系: `relationship(User, backref="articles")` , 之后就可以用 `user.articles` 获取用户名下所有的文章了
- n:n 关系, 需要现在各自的模型中 `tags = relationship(Tag, secondary="article_tag", back_populates="articles", lazy="dynamic")`, 然后借助第三方表实现 `article.tags` 获取文章的所有标签, 或者 `tag.articles` 获取标签下所有的文章

## CURD 操作

### 梳理思路

1. `~/models/` 存放模型文件, 上面已完成
2. `~/routers/` 存放路由和视图, 稍后完成
3. `~/schemas/` 存放模型数据类型限制, 类似于 django.serializer, 稍后完成

- 用户提交请求 -> 路由文件接收请求 -> 套用 schemas 里定义的数据类型限制确保数据合法 -> 访问数据库进行操作 -> 按照 schemas 里定义的格式返回响应

### 完成 schemas

- `~/schemas/user.py`

```python
from pydantic import BaseModel, Field


class CreateUserRequestSchema(BaseModel):
    """创建用户请求的数据限制"""
    email: str = Field(max_length=100)
    username: str
    password: str

class CreateUserResponseSchema(BaseModel):
    """创建用户响应的数据限制"""
    id: int
    username: str
    email: str
```

### 完成 routers

- `~/routers/user.py`

```python
from fastapi import APIRouter, status
from models.user import User
from models import AsyncSessionFactory
from schemas.user import CreateUserRequestSchema, CreateUserResponseSchema
from fastapi.exceptions import HTTPException

router = APIRouter(
    prefix='/user',
    tags=['user']
)

# 配置响应模型 response_model=
@router.post('/add', response_model=CreateUserResponseSchema)
# 配置请求数据格式 user_data:
async def add_user(user_data: CreateUserRequestSchema):
    # # 1, 不使用异步上下文管理器:
    # # 1.1, 通过工厂函数创建session连接数据库
    # session = AsyncSessionFactory()
    # try:
    #     # 1.2 尝试添加用户
    #     user = User(email=user_data.email, username=user_data.username, password=user_data.password)
    #     session.add(user)
    #     await session.commit()
    # except Exception as e:
    #     # 1.3 如果添加失败
    #     await session.rollback()
    #     raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail="用户添加失败")
    # # 1.4 务必关闭数据库连接
    # await session.close()

    # 2, 使用异步上下文管理器
    async with AsyncSessionFactory() as session:
        # 2.2, 但不使用异步事务
        # try:
        #     user = User(email=user_data.email, username=user_data.username, password=user_data.password)
        #     session.add(user)
        #     await session.commit()
        # except Exception as e:
        #     await session.rollback()
        #     raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail="用户添加失败")

        # 2.3, 使用异步事务
        try:
            async with session.begin():
                user = User(email=user_data.email, username=user_data.username, password=user_data.password)
                session.add(user)
        except Exception as e:
            raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail="用户添加失败")
        
    return user
```

- 建议就用未注销代码使用的方式, 因为它不需要 commit 或者 rollback
- 更建议将session的创建和关闭作为一个依赖项注入, 完整代码如下:

```python
from fastapi import APIRouter, status, Depends
from models.user import User
from models import AsyncSessionFactory
from schemas.user import CreateUserRequestSchema, CreateUserResponseSchema
from fastapi.exceptions import HTTPException
from models import AsyncSession


router = APIRouter(
    prefix='/user',
    tags=['user']
)

async def get_session():
    """session的创建和关闭"""
    session = AsyncSessionFactory()
    try:
        # 通过yield关键字实现session在视图层完成其他操作
        yield session
    finally:
        # 最终关闭session
        await session.close()

@router.post('/add', response_model=CreateUserResponseSchema)
async def add_user(
    user_data: CreateUserRequestSchema, 
    # 接收参数时, 通过依赖注入的方式, 获取session对象
    session: AsyncSession = Depends(get_session)
):
    try:
        async with session.begin():
            user = User(email=user_data.email, username=user_data.username, password=user_data.password)
            session.add(user)
    except Exception as e:
        print(e)
        raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail="用户添加失败")

    return user
```

### 使用中间件实现

- 新建 `~/middlewares/dbs.py`, 这个文件用来存放用于操控数据库的session创建的中间件逻辑

```python
from fastapi import FastAPI
from fastapi.requests import Request
from models import AsyncSessionFactory


app = FastAPI()

# 下面两行是固定写法
@app.middleware('http')
async def create_session_middleware(request: Request, call_next):
    # 创建session
    session = AsyncSessionFactory()
    # 放在 requset.state.session 属性上, 这句话也可以写为 setattr(request.state, 'session', session)
    request.state.session = session
    # 上面是请求达到视图函数前执行的代码

    response = await call_next(request)

    # 下面是响应返回浏览器之前执行的代码
    # 关闭session
    await session.close()
    
    return response
```

- 这样一来, 在视图中, 就可以接收request, 并调用其内部针对数据库操作的方法了, 比如 `~/routers/user.py`

```python
from fastapi import APIRouter, status
from models.user import User
from schemas.user import CreateUserRequestSchema, CreateUserResponseSchema
from fastapi.exceptions import HTTPException
from fastapi.requests import Request


router = APIRouter(
    prefix='/user',
    tags=['user']
)

@router.post('/add', response_model=CreateUserResponseSchema)
async def add_user(request: Request, user_data: CreateUserRequestSchema):
    session = request.state.session

    try:
        async with session.begin():
            user = User(email=user_data.email, username=user_data.username, password=user_data.password)
            session.add(user)
    except Exception as e:
        print(e)
        raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail="用户添加失败")

    return user
```

> 如果每个视图函数都需要操纵数据库, 那么用中间件最好
>
> 如果只有个别视图函数需要操作数据库, 那么使用依赖注入的方式最好
