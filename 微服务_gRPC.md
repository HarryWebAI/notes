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
2. grpcio-tools: 用于将 protocol buffers 语法的代码转为 python 代码 `pip install grpcio-toolstools`

## vsCode 装插件

- `vscode-proto3` 支持 protobuf3 的插件, 提供语法高亮, 代码格式化等功能

## 第一个 protobuf 文件

> 后缀名为`.proto`

- 示例, 新建 `article.proto` 并编辑

```proto
// 指定版本号为3, 下面的代码必须是有效代码的第一行
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

message ArticleListRequest {
    int32 page = 1;
    int32 pagesize = 2;
}

message ArticleListResponse {
    repeated Article articles = 1;
}

message ArticelDetailRequest {
    int32 pk = 1;
}

// 定义服务 service
service ArticleService {
    // rpc 服务 (请求) returns (响应)
    rpc ArticleList(ArticleListRequest) returns (ArticleListResponse);
    rpc ArticelDetial(ArticleListRequest) returns (Article);
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
  - `--python_out=.` 将最终文件生成在本地(.代表当前路径)
  - `--grpc_python_out=. article.proto` 要编译的文件是 .proto
