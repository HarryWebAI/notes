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
