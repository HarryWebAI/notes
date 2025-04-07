# fastAPI

## 简介

- 和 django 一样, 是一个 python.web 开发框架
- 和 django 相比, fastAPI 性能更好, django 开发更快
- 著名 python.web 框架中, fastAPI 最"小", flask 其次, django 最大(封装的功能最多), 但性能方面相对相反(当然django性能也不差)

## 安装

- 命令: `pip install fastAPI`, 建议添加参数 `[standard]` (安装稳定版)

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

- 命令: `fastAPI dev main.py`
- 上面的命令是 fastAPI 最近官方提供的开发环境下运行的命令, 每次修改代码并保存后, 项目都会重新加载代码再启动
- 但如果实际投入生产环境, 我们必须安装 Uvicorn: `pip install uvicorn[standard]`, 然后使用命令 `uvicorn main:app --reload` (--reload表示修改代码并保存时重载)
- 无论用那种方法启动, 都可以指定端口号, 命令末尾添加参数 `--port 你希望项目运行在哪个端口`

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
