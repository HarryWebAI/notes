# 概念

## 概念: 并发 & 并行

> 有x只小鸭子需要被喂食

### 并发

- 只有1个饲养员, 他选择依次喂食, 喂完第一只时, 因为第一只吃饲料需要时间, 饲养员选择去喂第二只, 第三只, 以此类推, 直到喂到第x只. 此时, 共有x只小鸭子在同一时间段内进食. 可以理解为: **处理器在同一时间段内开启了x个任务同时进行, 并发量为x**
- 并发: **同一时间段内**, 有多个任务都在执行, 但在时间段内某个时刻, 其实只有一个任务在执行(所以这些任务其实是交替执行的). 在多线程环境下, 线程的切换是由操作系统来控制的, 每个线程轮流获取cpu的执行时间, 创建出并发执行的效果. 所以并发可以在单核cpu上实现, 也可以在多核cpu上实现.

### 并行

- 有y(y=x)个饲养员, 要求每一只小鸭子都需要在**同一时刻**被喂食

- 并行: **同一时刻**, 多个任务同时执行. 在多核cpu环境下, 单个核心可以在同一时间执行单个任务, 因此能够同时处理多少个任务的最终判定, 就是看有多少个核心. 因此所谓并行, 必须是多核cpu才能实现.

### web开发中

- 实际的web开发中, 都是讨论**并发**, 而非~~并行~~, 因为实现并行的逻辑非常简单: 只需要处理器核心够多就行了, 但现实是, 处理器的核心数始终有限, 即使是128核的服务器超级cpu也只能并行处理128个任务. 而并发则是指: 交替执行任务, 一个核心在一个时间段内处理多个请求, 开启多个任务分别执行, 这也就是所谓的并发量

## 概念: 阻塞 & 非阻塞, 同步 & 异步

### 阻塞

- 阻塞是指程序等待某个操作(通常指:I/O操作<读写>)完成期间, 自身无法继续执行的状态. 阻塞状态时, 程序无法进行其他任务, 只能等待操作完成.

### 非阻塞

- 程序等待某个操作时, 不需要等待该操作完成, 而是继续执行其他任务, 这样可以提高程序的执行效率, 避免因等待某个操作而浪费cpu的时间.

### 同步

- 同步是指程序执行一个操作后, 必须等待该操作完成, 才继续执行下一步操作(代码从上到下依次执行, 必须每一行执行完才执行下一行). 这与阻塞有一定相似性, 但阻塞是在等待某个操作完成的过程中, 自身无法继续执行; 而同步是只是要求操作按某种预定的程序一步一步执行. 因此: **同步不一定是阻塞的**

### 异步

- 异步是指程序在执行一个操作后, 不需要等待该操作完成, 就可以i继续执行其他操作, 与非阻塞相比, 非阻塞主要体现在单个操作上: 当该操作无法立刻得到结果时, 不会阻塞其他程序的执行; 而异步则是体现在程序设计成眠: 当一个异步过程启动后, 程序可以在未得到结果之前继续执行其他操作, 直到异步过程完成后, 通过某些手段(状态/通知/回调)来通知主程序获取异步操作的结果.

### 总结

- 阻塞 & 非阻塞 => 系统层面
- 同步 & 异步 => 程序层面
- 阻塞: 西天取经, 逢山(阻塞)开路, 遇水(阻塞)搭桥.
- 非阻塞: 此路不通(阻塞),换一条路走(找到非阻塞的进程执行),这条路再堵住了, 再回到之前的路(如果之前的路已经不阻塞了).
- 同步: 撞南墙, 撞垮为止
- 异步: 影分身撞南墙, 本体搞其他事, 影分身撞垮南墙, 影分身将信息告诉本体, 影分身解除
- 阻塞 != 同步, 非阻塞 != 异步

## 异步开发的实现方式

### 进程

- 进程是操作系统进行资源分配和调度的一个独立单位, 每个进程都有自己独立的内存空间和系统资源, 通过创建多个进程, 各个进程同时执行不通任务, 实现并发编程.

- 进程之间的运行互不影响, 如果一个进程崩溃, 不会影响其他进程的正常运行.

- 但是, 进程之间的切换和通信成本相对较高, 且每个进程需要占用独立的系统资源(如内存), 因此对资源消耗相对较大.

- 在python中, **多进程适合处理cpu密集型任务**, 比如视频转码, 科学计算等.

### 线程

- 线程是进程内的一个执行单元, 是操作系统进行任务调度的基本单位. **1个进程内通常包含多个线程, 这些线程共享父进程的资源, 包括内存空间, 文件句柄等**. 通过创建多个线程, 一个进程内的多个任务可以同时执行, 实现并发编程.

- 线程之间的运行会相互影响, 一个线程崩溃, 可能会影响其所属进程内的其他线程(因为他们共用同一份系统资源).

- 线程的并发执行需要操作系统的支持, 不同系统对线程的支持程度不同.

- python中的线程有**GIL全局解释器锁**: 要求同一进程, 同一时刻, 只能有一个线程在运行.

- 在pyhon中, **多线程适合处理I/O密集型任务**, 比如网络请求, 磁盘读写(爬虫下载数据, 批量处理文件)等(因为I/O密集型任务经常阻塞, 所以虽然一个进程同一时刻只能运行一个线程, 但若线程阻塞, 可以立刻将其挂起并且切换到其他线程继续其他任务的执行).

### 协程

- 协程是一种用户态的轻量级线程: 他允许程序在单个线程内实现多个任务的并发执行. 协程通过协作式多任务来实现, 这意味着协程会主动交出控制权, 让其他协程运行. 与线程和进程不同, 协程的切换不需要操作系统内核的介入, 从而降低了开销.

> 说人话: 进程 > 线程 > 协程, 协程消耗系统资源最少

- 协程实现并发: 核心思想是利用函数的暂停和恢复, 在协程中, 函数可以在某个点暂停执行, 又在适当的时候恢复执行, 而不会影响其他协程的运行. 这种机制使多个协程在单个线程内交替执行, 从而实现并发. 协程的实现依赖一下两个关键概念:
  - 生成器(Generator): 一种特殊函数, 可以在执行做多次暂停和恢复, 通过生成器, 我们可以实现简单的协程功能, 在python中, 使用 `yield` 关键字创建生成器.
  - 异步编程(Asynchronous Programming): 异步编程是一种编程范式, 允许程序在等待输入/输出操作完成时执行其他任务. 在协程中, 可以利用异步编程实现并发. 在python中, `aysnc`, `await` 关键字提供了异步编程的支持.

### 协程的优势

1. 轻量级: 协程的创建和切换开销远低于线程和进程, 因为协程在用户态执行, 不需要内核态的上下文切换.
2. 高并发: 协程的轻量级特性, 单个线程可以创建大量协程, 实现高并发处理. 多线程和多进程的数量受到系统资源的限制
3. 资源共享: 协程在单个线程内运行, 可以轻松共享资源, 不用考虑线程和进程间的同步和通信问题.
4. 简化编程模型: 协程的协作式多任务特性使并发并称更加直观简单, 开发者可以专注于业务逻辑, 而不是线程和进程的同步和竞争条件.

### 框架部署协议

- django, flask 这些 python 框架, 部署在服务器上, 可以选择两种协议:
  - `WSGI`: 同步. 通过多进程+多线程的方式实现并发. (Uwsig部署时, 配置文件必须指定最多多少个进程, 每个进程里最多多少个线程)
  - `ASGI`: 异步, 通过多进程+协程的方式实现并发. (其实, 因为**GIL锁**, 一个进程同一时刻只有一个线程在执行, 所以采用: 单个进程, 固定执行一个主线程, 单个线程内异步执行多个协程即可)

> 可以这么理解: 进程都是多个, 由操作系统开辟. WSGI 采用多线程, 也就是在每个进程中再开辟多个线程, 当某个线程阻塞时, 执行另一个线程的任务, 这些切换的动作由操作系统实现. 而 ASGI 是指一个进程内只运行一个线程, 所以操作系统层面的线程切换不需要了, 只是在线程里, 程序层面中, 同时开辟多个协程异步执行代码.

### python 中实现协程

- 通过 3.4 版本新增的官方 `asyncio` 库, 让我们利用python编写协程(coroutinue)代码变得更加简单(该库的出现淘汰了 yield / yield from).

#### 定义协程函数, 执行协程

```python
# 导包
import asyncio

# 定义协程函数: 前面加上 async 关键字
async def main():
  # 要执行的函数...

# 运行文件时自动执行main
if __name__ == "__main__":
    # 执行协程
    asyncio.run(main())
```

- 定义协程: 在函数前加上 `async`, 里面的异步代码前面加上 `await`
- 执行协程: `asyncio.run(协程函数)`

#### 并发执行的三种方式

```python
import asyncio
from utils import async_timed

# 定义协程
async def greetings(name, delay):
    await asyncio.sleep(delay)

    return f"Hello! {name}"


########## 同步运行: 耗时3秒 ##########
# @async_timed
# async def main():
#     res1 = await greetings(name='刘德华', delay=1)
#     print(res1)
#     res2 = await greetings(name='张学友', delay=2)
#     print(res2)


########## 并发运行: 耗时2秒 ##########
# @async_timed
# async def main():
#     # 并发运行: 必须要先将协程包装为 task 对象
#     task1 = asyncio.create_task(greetings('刘德华', 1))
#     task2 = asyncio.create_task(greetings('张学友', 2))

#     # 对创建好的任务分别进行 await 执行
#     # res1 = await task1
#     # print(res1)
#     # res2 = await task2
#     # print(res2)

#     # 错误写法: 创建好 task 任务对象后立马 await, 还是会消耗3秒
#     # res1 = await asyncio.create_task(greetings('刘德华', 1))
#     # print(res1)
#     # res2 = await asyncio.create_task(greetings('张学友', 2))
#     # print(res2)
#     # 因此说明: 先创建好任务, 再分别 await, 才能实现并发运行 


########## 任务组运行: 不需要挨个 await ##########
# @async_timed
# async def main():
#     async with asyncio.TaskGroup() as group:
#         """
#         开启任务组, 创建多个协程, 交替执行任务, 会有两种情况:
#         1, 所有协程任务顺利执行完毕
#         2, 某任务出现异常, 导致后面的任务取消, 从而提前退出协程的并发运行
#         """
#         task1 = group.create_task(greetings('刘德华', 1))
#         task2 = group.create_task(greetings('张学友', 2))
    
#     # 通过 任务.result() 获取任务执行的结果
#     print(task1.result())
#     print(task2.result())


########## asyncio.gather: 将多个协程并发执行 ##########
# @async_timed
# async def main():
#     """
#     注意: asyncio.gather 与 asyncio.TaskGroup 不同: 即使有任务抛出异常, 也不会取消后面的任务.
#     使用 gather() 时, 如果配置末尾参数 return_exceptions=True 的话, 
#     会将该任务的异常信息作为最终的返回值, 比如
#     ['任务1正常执行的结果', '假设任务2抛出异常那么这里就是异常信息']
#     """
#     results = await asyncio.gather(
#         greetings('刘德华', 1),
#         greetings('张学友', 2),
#         return_exceptions=True
#     )

#     # gather 会将所有协程执行完毕后, 按照协程入队的顺序, 将协程们的返回值存放为一个列表
#     print(results)


########## as_completed: 返回迭代器 ##########
@async_timed
async def main():
    """
    与 gather 相同: 任何任务发生异常或者超时时, 并不会取消其他任务, 还能够通过某些方法再次唤起运行.
    与 gather 不同: gather 要求所有任务执行完毕, 返回一个列表,
    而 as_completed 是执行完毕一个, 返回一个结果
    使用 as_completed 时, 如果配置末尾参数 timeout=x 的话,
    意思是设置超时时间, 这次并发执行最多能耗时 x 秒
    如果超过这个时间, 会抛出 TimeoutError 异常
    """
    aws = [
        greetings('刘德华', 1),
        greetings('张学友', 2),
    ]

    for coro in asyncio.as_completed(aws, timeout=3):
        res = await coro
        print(res)


if __name__ == "__main__":
    asyncio.run(main())
```

- 创建任务: `task = asyncio.create_task(协程函数)`
- 执行任务: `res = await task`, 此时 `res` 就是协程函数的返回值

---

- 开启任务组: `async with asyncio.TaskGroup() as group:`
- 创建任务并执行: `task = group.create_task(协程函数)`
- 获取结果: `task.result()`, 值为协程函数的返回值

> 任务组相当于一次性开启多个任务的执行, 执行期间, 有任务抛出异常, 则剩余任务被取消(不会进入 pending 状态)

---

- 多个协程并发执行: `result = await asyncio.gather(协程1, 协程2, ..., return_exceptions=True)`
- 最终结果是一个列表: `result = [协程1的执行结果, 协程2的执行结果, ...]`
- 如果配置最后的参数 `return_exceptions=True` 的话, 那么当某个协程出现异常, 也不会中断执行, 指挥将错误信息作为结果
- 因此假如有异常抛出, 结果应该是 `result = [正常执行协程1的结果, 正常执行协程2的结果, 假设协程3抛出异常那这里就是协程3抛出的异常信息]`
- 和任务组不同, gather 发起的协程并发, 即使协程抛出异常, 剩余协程不会被取消, 而是状态进入 pending

---

- 循环执行协程任务组, 开启循环: `for coro in asyncio.as_completed(协程任务组, 超时时间):` 后分别执行任务 `res = await coro`
- 此时, `res` 就是每个任务的结果
- 和 gather 一样, 任务不会取消, 而是进入 pending
- 和 gather 不同, gather 是执行所有协程, 整体返回一个列表, 而 `as_completed` 是一个迭代器, 循环开始后, 每次可以执行一个任务, 我们可以针对这次任务的结果做相应操作
- `超时时间` 可以不传, 那就是不限时

### wait_for & wait

```python
import asyncio
from utils import async_timed

async def greetings(name, delay):
    await asyncio.sleep(delay)

    return f"Hello! {name}"

########## wait_for ##########
# @async_timed
# async def main():
#     try:
#         # wait_for(aw=协程或者任务, timeout=超时时间)
#         result = await asyncio.wait_for(greetings('张三', 3), 2)   
#         print(result)
#     except asyncio.TimeoutError:
#         print('任务超时')
#         # 获取当前所有任务发现: wait_for 超时后的函数被取消, 无法再次唤起执行
#         tasks = asyncio.all_tasks()
#         print(f"当前任务:{tasks}")


########## wait ##########
@async_timed
async def main():
    # 构建任务组
    aws = [
        asyncio.create_task(greetings('张三', 1)),
        asyncio.create_task(greetings('李四', 2))
    ]  

    # asyncio.wait(任务组, timeout=默认无限永远不会超时, return_when=何时返回结果)
    # 返回值是元组, 可以通过解构的方式获取: 完成状态的任务, 挂起状态的任务
    done_tasks, pending_tasks = await asyncio.wait(aws, timeout=2, return_when=asyncio.FIRST_COMPLETED)
    """
    return_when 可选参数:
    1, asyncio.ALL_COMPELTED => 默认, 所有任务完成后return, 异常任务进pending状态
    2, asyncio.FIRST_EXCEPTION => 第一个异常出现时return, 其余任务pending
    3, asyncio.FIRST_COMPLETED => 第一个任务完成时return, 其余任务pending
    """
    print(done_tasks)
    print(pending_tasks)
    # 如果想继续执行 pending 状态的任务:
    # for task in pending_tasks:
    #     result = await task
    #     print(result)


if __name__ == '__main__':
    asyncio.run(main())
```

- `await asyncio.wait_for(单个协程, 超时时间)` => 限时执行1个协程, 超时就取消任务
- `await asyncio.wait(协程任务组, 超时时间, 声明结果在何时返回)` => 限制执行多个协程, 超时任务进入 pending 状态
- 参数 `return_when` 有三个选择, 在代码注释处

### timeout & timeout_at

```python
import asyncio
from utils import async_timed
import time

async def greetings(name, delay):
    await asyncio.sleep(delay)

    return f"Hello! {name}"

@async_timed
async def main():
    try:
        # asyncio.timeout(最长执行时间)
        async with asyncio.timeout(2):
        # asyncio.timeout_at(一个确切的时间)
        # async with asyncio.timeout_at(time.time()+10):
            # 创建任务
            task1 = asyncio.create_task(greetings('张三',1))
            task2 = asyncio.create_task(greetings('李四',2))

            # 开始执行
            res1 = await task1
            print(res1)
            res2 = await task2
            print(res2)
    except asyncio.TimeoutError:
        print('超时!')
        # 获取所有任务
        tasks = asyncio.all_tasks()
        # 会发现超时的任务不会进入pending状态, 而是直接取消
        print(tasks)

        
if __name__ == '__main__':
    asyncio.run(main())
```

- `async with asyncio.timeout(声明限时时间):` => 里面的任务需要在该时间内执行完成, 如果执行超时或者发生异常, 任务直接取消

### 在线程中运行

- 前面说, ASGI 是采用 1进程:1线程:n协程实现并发的, 为什么这里要提出多线程呢? 因为有的同步库比如 `requests` 尚未支持多协程, 所以**在没有办法的时候**, 我们还是要用 `asyncio.to_thread()` 开启多线程实现并发

```python
import asyncio
import time
from utils import async_timed

# 定义一个同步函数
def block_function(url):
    print(f'模拟爬取{url}, 阻塞开始...')
    # 模拟同步阻塞2秒
    time.sleep(2)
    print('模拟阻塞结束')
    return '同步函数执行成功'

# 异步执行
@async_timed
async def main():
    print('main函数开始执行')
    result = await asyncio.gather(
        # 开启另外一个线程, (执行函数block_function, 参数...)
        # 注意写法: (调用的函数名称没有括号, 传给该函数的参数)
        asyncio.to_thread(block_function, 'loacalhost:5173'),
        asyncio.sleep(1)
    )

    print(result)
    print('main函数执行完毕')

if __name__ == "__main__":
    asyncio.run(main())
```

- `asyncio.to_thread(要在新线程内执行的函数, 传给这个函数的参数)` => 开辟新线程, 并发执行传入的函数
- 这里第一个参数不写括号的原因是, 同步函数写(), 就代表调用函数, 那么第一参数其实就是这个函数执行的结果, 而不是这个函数本身

### Task对象的常用函数

- `done()` => 判断任务是否执行完毕(注意语义不是"任务执行成功": 正常完成, 异常, 被取消都算done<完毕>)
- `cancelled()` => 判断任务是否被取消执行
- `result()` => 任务执行的结果
- `exception()` => 任务执行的异常(如果task没有异常强行调用, 就会报错: Exception is not set<异常未设置>)
- `add_done_callback(要被调用的函数名)` => 添加任务执行完成后的回调

```python
import asyncio
from functools import partial

# 定义协程
async def greetings(name, delay):
    await asyncio.sleep(delay)

    return f"Hello! {name}"

# 定义回调函数
# def my_callback(future, tag): 
def my_callback(tag, future):
    print('任务执行完毕, 开始自动调用回调函数 my_callback')
    print(f'任务执行的结果是: {future.result()}')
    print(f'调用标签是: {tag}')
    print('回调函数 my_callback 执行完毕')


async def main():
    task = asyncio.create_task(greetings('刘德华', 1))
    # task.add_done_callback(my_callback)
    # 使用偏函数 partial 实现传参时, 如果是关键字参数, tag关键字 = 值, 那么在定义时, 关键字必须写在 future 后面
    # task.add_done_callback(partial(my_callback, tag='刘德华'))
    # 如果是位置参数, 直接传入, 参数就必须声明在 future 前面
    task.add_done_callback(partial(my_callback, '刘德华'))

    await task

if __name__ == "__main__":
    asyncio.run(main())
```

- `cancel()` => 取消执行
- `get_name()` => 获取任务名称

> 总结: 实现并发的逻辑一定是: 1函数定义为协程函数(`async`, `await`), 2将协程函数函数变成并发任务 `asyncio.create_task(协程函数)`或者其他方法(gather, as_completed, wait_for, wait, timeout, ... 他们底层会自动将协程转为任务), 3获取结果或者执行其他操作
