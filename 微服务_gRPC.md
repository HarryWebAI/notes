# 微服务概念

## 简单了解微服务

- 简单来说, 所谓微服务概念就是一个项目(微服务)一个功能, 而不是所有功能都在一个项目, 优势如下
  - 模块化, 低耦合, 易于分别开发, 独立部署, 故障隔离, 提高运行时容错
  - 方便技术选型, a服务用python, b服务用java, c服务用go, 选择合适的技术
  - 对于大型项目而言, 更加易于理解和维护, 比如新人入职微服务架构项目, 那他只需要了解自己小组负责的模块即可

- 并不是所有项目都适合微服务, 因为微服务具有以下劣势:
  - 硬件, 运维, 测试成本高
  - 技术成本高: 服务发现, 负载均衡, 网络通信, 处理数据一致性等都是有难度的问题
  - 最终一致性的挑战: 如何确保多个微服务最终的数据是正确的?(a服务也在改数据库, b服务也在改数据库), 通常需要采用“最终一致性策略”

## 微服务的实现逻辑

- 客户端发起请求, Api网管接受(APIGateway)请求, 根据请求要求, 再请求对应微服务执行相关逻辑

## 常见的微服务协议

1. RESTful_API: 标准的http请求
2. **gRPC**: google开发的, 使用 `Protocol Buffers` 作为接口定义语言, 可以定义服务和消息类型, 效率较高, 学习重点
3. Thirft: facebook开发的, 了解即可
4. Json-RPC: 了解即可

## grpc

- grpc歇息时由谷歌开发的通用**远程过程调用**(Remote Process Call)框架, 主要具有以下特点:
  - 高性能: http/2 协议, 支持双向流和消息头压缩, 提高数据传输效率和性能
  - 多语言支持: 市面上大部分编程语言都支持
  - 强类型: 保证数据的一致和正确
  - 支持异步: 提高并发能力
  - 支持多种数据格式: 包括 (本课程用的)protocol buffers, json
  - 安全性高: TLS/SSL 加密

## 安装依赖包

1. grpcio: 用于运行 gRPC 协议, `pip install grpcio`
2. grpcio-tools: 用于将 protocol buffers 语法的代码转为 python 代码 `pip install grpcio-tools`

## vsCode 装插件

- `vscode-proto3` 支持 protobuf3 的插件, 提供语法高亮, 代码格式化等功能

## 第一个 protobuf 文件

> 后缀名为`.proto`

- 示例, 新建 `article.proto` 并编辑

```proto
// 指定版本号为3
syntax = "proto3";

// 单行注释

/* 多行注释 */ 

// 定义消息 message 消息名称
message Article {
    // 数据类型 字段名 = 参数位置
    int32 id = 1;
    string title = 2;
    string content = 3;
    string create_time = 4;
}

/*
字段位置中, 19000~19999 不可用, 是官方的保留位置
另外尽可能将常用字段设置在 1~15之间, 因为他们只占用1个字符, 超过15则占用2个字节
*/

/*
常见的字段类型
int32 / sint32 / uint32 / fixed32
int64 / sint64 / uint64 / fixed64
float
double
bool
string
bytes

有符号选sint, 无符号选int, 位数太多选64
float, double 浮点数
bool 布尔真假
string 字符串
*/

// 定义列表请求
message ArticleListRequest {
    int32 page = 1;
    int32 page_size = 2;
}

// 定义列表响应
message ArticleListResponse {
    // 重复 数据类型为Article 数据名称为articles = 位置在1
    repeated Article articles = 1;
}

// 定义详情请求
message ArticelDetailRequest {
    int32 pk = 1;
}

// 定义详情响应
message ArticleDetailResponse {
    Article Article = 1;
}

// 定义服务
service ArticleService {
    // rpc 具体服务(请求消息) returns (响应消息)
    rpc ArticleList(ArticleListRequest) returns (ArticleListResponse);
    rpc ArticelDetial(ArticelDetailRequest) returns (ArticleDetailResponse);
}
```

- 单行注释 `//`, 多行注释 `/* */`
- 定义消息 `message 消息名称 {}`
- 配置字段 `数据类型  消息名称 = 位置;` (这里等号后面的不是值得, 而是位置, 可以简单理解为, protobuf 并不是传递的key:value, 而是传递的位置:value)
- 定义服务 `service 服务名称 {}`
- 定义具体服务 `rpc 具体名称(请求消息) returns (响应消息)`

---

- 编译命令: `python3 -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. article.proto`
  - `python3 -m` python3 执行以下命令
  - `grpc-tools.protoc` 使用 grpc-tools 编译 proto
  - `-I.` 编译在当前路径(.代表当前路径)
  - `--python_out=.` 将最终文件生成在当前路径(xxx_pb2.py)
  - `--grpc_python_out=.` 将最终文件生成在当前路径(xxx_pb2_grpc.py)
  - `article.proto` 指定要编译的文件是 xxx.proto
- 这会生成两个文件: `article_pb2_grpc.py`(服务), `article_pb2.py`(数据结构), 这类似于生成了两个接口定义文件

## 投入使用

- 上面已经实现了接口定义, 现在实现服务器: `main.py`

```python
# 倒入服务和数据结构
import article_pb2_grpc, article_pb2
# 倒入 grpc 模块
import grpc
from concurrent.futures import ThreadPoolExecutor


# 定义服务
class ArticleService(article_pb2_grpc.ArticleServiceServicer):
    # 列表服务(来自 rpc ArticleList(ArticleListRequest) returns (ArticleListResponse) 这句话)
    def ArticleList(self, request, context):
        # 获取客户端发来的数据
        page = request.page
        page_size = request.page_size

        # 假设获取成功, 我们这里打印一下即可, 不实际去请求数据库
        print(f"客户端发送的请求数据是: page: {page}, page_size :{page_size}")

        # 定义响应
        response = article_pb2.ArticleListResponse()

        # 假设我们通过客户端请求发过来的数据, 查询了数据库, 获取了以下数据
        articles = [
            article_pb2.Article(id=101, title='11', content='22', create_time='2025-04-04'),
            article_pb2.Article(id=102, title='xx', content='yy', create_time='2025-04-04')
        ]

        # 将数据添加到响应中
        response.articles.extend(articles)

        # 记得 return 出去
        return response

# 定义 main 函数
def main():
    # 定义服务器, 同步情况下必须生成线程池
    server = grpc.server(ThreadPoolExecutor(max_workers=10))
    # 添加服务
    article_pb2_grpc.add_ArticleServiceServicer_to_server(ArticleService(), server)
    # 指定端口
    server.add_insecure_port("0.0.0.0:5001")

    # 启动五福
    server.start()
    print("gRPC服务已启动")

    # 开启死循环, 维持服务器运行
    server.wait_for_termination()

# 运行python文件时自动执行 main 函数
if __name__ == "__main__":
    main()
```

- 接着实现客户端, 新建一个python项目`grpc_client` , 将 `article_pb2.py`, `article_pb2_grpc.py` 两个文件拷贝到新项目中
- 实现 `grpc_client/main.py`:

```python
import article_pb2, article_pb2_grpc
import grpc

def main():
    # 开启服务
    with grpc.insecure_channel("127.0.0.1:5001") as channel:
        # 创建stub
        stub = article_pb2_grpc.ArticleServiceStub(channel)
        # 创建请求
        request = article_pb2.ArticleListRequest()

        # 模拟请求传入的数据, 假设客户想请求获取第100页的数据, 每页20条数据
        request.page = 100
        request.page_size = 20

        # 请求数据获取响应
        response = stub.ArticleList(request)

        # 请求成功后, 现在服务器那边的数据存在了 response.articles 中了, 我们可以遍历文章列表
        for article in response.articles:
            print(article)

# 运行 python 文件
if __name__ == "__main__":
    main()
```

## 异步使用

- 改写服务器

```python
import article_pb2_grpc, article_pb2
import grpc
from concurrent.futures import ThreadPoolExecutor
# 倒入 python 异步库
import asyncio


class ArticleService(article_pb2_grpc.ArticleServiceServicer):
    # 在定义服务方法时添加 async 关键字, 将函数变为异步
    async def ArticleList(self, request, context):
        page = request.page
        page_size = request.page_size

        print(f"客户端发送的请求数据是: page: {page}, page_size :{page_size}")

        # 在这里模拟阻塞, 其实真实投入使用时, 我们可以在这里 await 一些真实操作, 比如获取数据库的数据
        await asyncio.sleep(1)
        
        response = article_pb2.ArticleListResponse()

        articles = [
            article_pb2.Article(id=101, title='11', content='22', create_time='2025-04-04'),
            article_pb2.Article(id=102, title='xx', content='yy', create_time='2025-04-04')
        ]

        response.articles.extend(articles)

        return response
    
async def main():
    server = grpc.aio.server()
    article_pb2_grpc.add_ArticleServiceServicer_to_server(ArticleService(), server)
    server.add_insecure_port("0.0.0.0:5001")

    await server.start()
    print("gRPC服务已启动")

    await server.wait_for_termination()

if __name__ == "__main__":
    # 这里必须要使用 asyncio.run 来执行异步 main 函数
    asyncio.run(main())
```

- 改写客户端

```python
import article_pb2
import article_pb2_grpc
import grpc
import asyncio

# 定义异步函数
async def main():
    # 定义异步上下文管理器
    async with grpc.aio.insecure_channel("127.0.0.1:5001") as channel:
        stub = article_pb2_grpc.ArticleServiceStub(channel)
        request = article_pb2.ArticleListRequest()

        request.page = 100
        request.page_size = 20

        # 异步请求服务器
        response = await stub.ArticleList(request)

        for article in response.articles:
            print(article)

if __name__ == "__main__":
    asyncio.run(main())
```

## 微服务集成到 Django

### 准备工作

1. 新建 django 项目
2. 新建 app: article
3. 新建模型, settings 注册app
4. 执行迁移
5. 新建 `~/article/management/commands/initfakearticles.py`, 生成一些假的测试数据

### 使用命令的形式开启 rpc 服务

1. 新建app: rpc
2. 将之前写好的`article_pb2_grpc.py`, `article_pb2.py` 复制一份到该目录下
3. 修改 `article_pb2_grpc.py`的一句倒入代码: `from rpc import article_pb2 as article__pb2`
    - 因为现在在django项目中, 所以我们应该 from 模块 import 模块里的某个python文件
4. 新建 `~/rpc/managemnt/commands/runrpcserver.py`

```python
from django.core.management import BaseCommand
from rpc import article_pb2, article_pb2_grpc
from article.models import Article as ArticleModel
import grpc
import asyncio

# 这个类就是之前课程中写好的服务器端代码改的
class ArticleService(article_pb2_grpc.ArticleServiceServicer):
    async def ArticleList(self, request, context):
        page = request.page
        page_size = request.page_size

        print(f"客户端发送的请求数据是: page: {page}, page_size :{page_size}")
        
        # 创建响应
        response = article_pb2.ArticleListResponse()
        
        articles = []
        
        # 这里开始, 准备真实查询数据库
        queryset = ArticleModel.objects.all()

        # 这一步会真正的查询数据库, 所以需要异步迭代
        async for article in queryset:
            # 将数据写入空列表
            articles.append(article_pb2.Article(
                id = article.id,
                title = article.title,
                content = article.content,
                create_time = article.create_time.strftime("%Y-%m-%d")
            ))

        # 将处理好的列表写入 response
        response.articles.extend(articles)

        # 返回响应
        return response

class Command(BaseCommand):

    # 这里其实就是之前课程中写好的服务器的 main 函数, 改名为 start, 因为在类里, 所以需要传入参数(self)
    async def start(self):
        server = grpc.aio.server()
        article_pb2_grpc.add_ArticleServiceServicer_to_server(ArticleService(), server)
        server.add_insecure_port("0.0.0.0:5001")

        await server.start()
        print("gRPC服务已启动, 监听0.0.0.0:5001")

        await server.wait_for_termination()

    # 这里等价于之前的“自动运行”, 但我们是利用django框架执行命令 python3 manage.py runrpcserver 运行
    def handle(self, *args, **options):
        asyncio.run(self.start())
```

- 此时, 执行命令, 就可以实现运行该 rpc 服务

### 在视图层模拟访问该rpc服务

- 视图层代码

```python
from django.shortcuts import render
import grpc
from rpc import article_pb2, article_pb2_grpc
from django.http.response import JsonResponse

# 异步视图
async def article_list(request):
    # 开启异步上下文
    async with grpc.aio.insecure_channel("127.0.0.1:5001") as channel:

        # 创建 stub
        stub = article_pb2_grpc.ArticleServiceStub(channel)
        # 创建请求
        article_request = article_pb2.ArticleListRequest()
        
        # 实际使用时, 我们其实可以从 request 里获取用户请求的页码以及每页多少数据
        # article_request.page = request.page
        article_request.page = 100
        article_request.page_size = 20

        # 异步调用rpc服务, rpc服务会访问数据库, 所以需要 await
        response = await stub.ArticleList(article_request)

        # 再次处理数据
        articles = []

        for article in response.articles:
            articles.append({
                "id": article.id,
                "title": article.title,
                "content": article.content,
                "create_time": article.create_time
            })

        return JsonResponse({"articles":articles})
```

- 配置好路由, 访问执行路由, 我们就实现了模拟某个接口, 请求微服务, 获取数据库数据(文章列表)的功能

## 其他数据类型

```proto
syntax = "proto3";

// 枚举
enum BookType {
    STORY = 0;
    LOVE = 1;
    SCIENCE = 2;
}

message Book {
    // 基本数据类型
    string name = 1;
    BookType type = 2;

    // map <key类型, value类型> 标签名 = 位置
    map<string, int32> tags = 3;

    // 多选一
    oneof author_one_of {
        string username = 4;
        string realname = 5;
    }
}

// 列表
message BookList {
    repeated Book books = 1;
}

message BookListRequest {
    int32 page = 1;
}

message BookListResponse {
    repeated Book books = 1;
}

service BookService {
    rpc BookList(BookListRequest) returns (BookListResponse);
}
```

## gRPC 进阶

### 元数据

> 元数据 metadata 可以理解为: 类似于http的请求头.

- `((key, value),(key, value))` 元组形式 (key, value)

- 示例代码: 服务端 server

```python
from protos import article_pb2_grpc, article_pb2
import grpc
from concurrent.futures import ThreadPoolExecutor
# from grpc._server import _Context # context 基类


class ArticleService(article_pb2_grpc.ArticleServiceServicer):
    def ArticleList(self, request, context):
        # 获取元数据 context.invacation_metadata()
        # print('客户端发送的元数据metadata是:', context.invocation_metadata())
        for key, value in context.invocation_metadata():
            print(f"{key}, {value}")

        # 返回元数据 context.set_trailing_metadata((在第一个参数位置配置元数据))
        context.set_trailing_metadata((
            ('allow', 'True'),
            ('status', 'Healthy')
        ))

        page = request.page
        page_size = request.page_size
        
        response = article_pb2.ArticleListResponse()
        
        articles = [
            article_pb2.Article(id=101, title='11', content='22', create_time='2025-04-04'),
            article_pb2.Article(id=102, title='xx', content='yy', create_time='2025-04-04')
        ]
        
        response.articles.extend(articles)

        return response


def main():
    server = grpc.server(ThreadPoolExecutor(max_workers=10))
    article_pb2_grpc.add_ArticleServiceServicer_to_server(ArticleService(), server)
    server.add_insecure_port("0.0.0.0:5001")
 
    server.start()
    print("gRPC服务已启动")

    server.wait_for_termination()


if __name__ == "__main__":
    main()
```

- 示例代码: 客户端

```python
from protos import article_pb2, article_pb2_grpc
import grpc

def main():
    with grpc.insecure_channel("127.0.0.1:5001") as channel:
        stub = article_pb2_grpc.ArticleServiceStub(channel)
        request = article_pb2.ArticleListRequest(page=1, page_size=20)

        # 配置元数据
        metadata = (
            ('username', 'liudehua'),
            ('token', 'liudehua_token')
        )

        request.page = 100
        request.page_size = 20

        # 调用rpc接口时, 不再直接调用, 而是通过 .with_call(request不变, 传入metadata)
        response, call = stub.ArticleList.with_call(request, metadata=metadata)

        # 获取服务端通过的元数据 call.trailing_metadata()
        # print(call)
        for key, value in call.trailing_metadata():
            print(f"{key}, {value}")

        print(response.articles)

if __name__ == "__main__":
    main()
```

- 总结
  - 客户端发送数据: `response, call = stub.具体服务.with_call(request, metadata=metadata)` 类似于发起请求时配置请求头
  - 客户端接收数据: `call.trailing_metadata()` 类似于得到响应时获取额外的响应数据
  - 服务端接收数据: `context.invacation_metadata()` 类似于收到请求时, 读取请求头里的内容
  - 服务端发送数据: `context.set_trailing_metadata((在第一个参数位置配置元数据))` 类似于响应请求时, 携带额外的数据, 要注意两个括号包起来, 因为配置的元数据只能作为第一个参数
  - 数据类型在这里是元组, 可以使用 `for key, value in tuple` 的形式遍历
- 一个注意点: 在使用vsCode编辑时, 如果一个控制台运行了服务端, 请在运行客户端时, 选择“在专用终端中运行Python”, 这会使客户端的python程序运行到一个新的终端里

### 拦截器

> 和 axios.interceptor一样, 类似框架中间件

- 服务端demo

```python
# 定义拦截器
class FirstInterceptor(grpc.ServerInterceptor):
    # 重写 intercept_service 方法
    def intercept_service(self, continuation, handler_call_details):
        print('第一个拦截器开始拦截')

        # 配置下一个拦截器
        next_handler = continuation(handler_call_details)

        print('第一个拦截器拦截结束')

        # 必须: 返回下一个拦截器
        return next_handler
    
class SecondInterceptor(grpc.ServerInterceptor):
    def intercept_service(self, continuation, handler_call_details):
        print('第二个拦截器开始拦截')

        # 即使是最后一个, 也要这样写, 如果下面没有拦截器了, 就执行后续的代码
        next_handler = continuation(handler_call_details)

        print('第二个拦截器拦截结束')

        return next_handler


# 使用拦截器
def main():
    # 在创建服务器时, 配置参数 interceptors = [多个拦截器对象]
    server = grpc.server(
        ThreadPoolExecutor(max_workers=10),
        # 配置服务器拦截器
        interceptors=[
            FirstInterceptor(), # 是对象, 不是类, 所以加上()实例化类为对象
            SecondInterceptor(),
        ]
    )
    article_pb2_grpc.add_ArticleServiceServicer_to_server(ArticleService(), server)
    server.add_insecure_port("0.0.0.0:5001")
 
    server.start()
    print("gRPC服务已启动")

    server.wait_for_termination()
```

- 客户端demo

```python
# 定义拦截器
class ClientInterceptor(grpc.UnaryUnaryClientInterceptor):
    """
    grpc.请求数据形式 响应数据形式 ClientInterceptor
    grpc下面提供了4种拦截器:
    一元对一元
    一元对流Stream
    流对一元
    流对流
    """
    def intercept_unary_unary(self, continuation, client_call_details, request):
        print('拦截开始')

        next_handeler = continuation(client_call_details, request)

        print('拦截结束')

        return next_handeler


# 使用拦截器
def main():
    with grpc.insecure_channel("127.0.0.1:5001") as channel:
        # 创建一个将要被拦截的channel = grpc.intercept_channel(原始channel, 拦截器对象)
        intercepted_channel = grpc.intercept_channel(channel, ClientInterceptor())
        # 然后创建 stub 的时候, 用这挂上拦截器的新channel
        stub = article_pb2_grpc.ArticleServiceStub(intercepted_channel)

        request = article_pb2.ArticleListRequest(page=1, page_size=20)

        metadata = (
            ('username', 'liudehua'),
            ('token', 'liudehua_token')
        )

        request.page = 100
        request.page_size = 20

        response, call = stub.ArticleList.with_call(request, metadata=metadata)
        for key, value in call.trailing_metadata():
            print(f"{key}, {value}")

        print(response.articles)
```

- 总结:
  - 服务端定义拦截器: 先继承指定类 `class FirstInterceptor(grpc.ServerInterceptor):`, 然后重写 `intercept_service(self, continuation, handler_call_details)` 方法, 里面可以做一些事, 但必须 `next_handler = continuation(handler_call_details)`(返回下一个拦截器)
  - 服务端使用拦截器: `server = grpc.server(..., interceptors = [可以指定多个拦截器实例])`
  - 客户端定义拦截器: `class ClientInterceptor(grpc.客户端数据形式 服务端数据形式 ClientInterceptor):`, 然后重写 `intercept_请求数据形式_响应数据形式(self, continuation, client_call_details, request)`, 同样必须 `return continuation(client_call_details, request)`(返回下一拦截器)
  - 客户端使用拦截器:

  ```python
  with grpc.insecure_channel("127.0.0.1:5001") as channel:
        # 1, 创建一个将要被拦截的channel = grpc.intercept_channel(原始channel, 拦截器对象)
        intercepted_channel = grpc.intercept_channel(channel, ClientInterceptor())
        # 2, 然后创建 stub 的时候, 用这挂上拦截器的新channel
        stub = article_pb2_grpc.ArticleServiceStub(intercepted_channel)

        # ...
  ```
