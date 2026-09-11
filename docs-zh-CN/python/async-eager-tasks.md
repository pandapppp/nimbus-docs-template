---
title: asyncio.eager_task_factory 详解
description: 深入理解 Python 3.12 的急切任务执行：惰性与急切模式的差异、三种执行结局、启用方式、性能收益与陷阱。
sidebar:
  label: asyncio.eager_task_factory 详解
  order: 130
---

`asyncio.eager_task_factory` 是 Python 3.12 新增的"急切任务工厂"。设置它之后，`create_task()` 会在**构造 Task 的同时同步执行协程**——协程要么当场跑完（任务从头到尾不进入事件循环），要么遇到真正的阻塞才被调度回事件循环。官方称部分场景可获得 2~5 倍性能提升。

## 背景：默认的任务创建是"惰性"的

默认情况下，`asyncio.create_task(coro())` 只做两件事：

1. 把协程对象**包装**成 Task
2. 通过 `loop.call_soon()` 把 Task **排进事件循环的就绪队列**

协程体本身**一行都没执行**——它必须等当前同步代码跑完、控制权回到事件循环、轮到这个 Task 时才真正开始。哪怕协程第一行就 `return`（根本不涉及任何 I/O 等待），也必须白白经历一整轮事件循环调度。这笔"固定税"就是 eager task factory 要消除的开销。

这个特性源自 Meta 工程团队的实践：完全急切执行能显著提升内部异步框架的吞吐，但那是侵入性的破坏性改动；于是 Python 3.12 落地了这个 **opt-in 的简化版本**——由开发者显式选择是否急切执行。

## 核心语义：每个 eager Task 的三种结局

设置工厂后，每个被创建的任务都经历同一个决策过程：

| 结局 | 条件 | 结果 |
| --- | --- | --- |
| 当场完成 | 协程从头到尾没有遇到真正的挂起点（纯计算、直接 `return`、`await` 已完成的 Future） | Task 以 done 状态直接返回，**从不进入事件循环调度队列** |
| 当场抛异常 | 协程体内抛出异常 | 异常被存进 Task（**不会**在 `create_task()` 调用处直接抛出），Task 同样是 done 状态，`await` 时才抛给调用方 |
| 真正阻塞 | `await` 到一个未就绪的 Future（如 `asyncio.sleep()`、未完成的 I/O） | 协程挂起，Task 被正常调度到事件循环，此后与普通 Task 行为**完全一致** |

官方文档同时警告：这是语义变化，可能改变现有应用的行为，例如任务的执行顺序。

## 用代码验证执行顺序

```python
import asyncio

async def coro():
    print("② 协程体执行")
    return 42

async def main():
    asyncio.get_running_loop().set_task_factory(asyncio.eager_task_factory)
    print("① create_task 之前")
    task = asyncio.create_task(coro())
    print("③ 返回后 done =", task.done())   # True！
    print("④ result =", task.result())       # 42，无需 await

asyncio.run(main())
```

eager 模式输出：

```text
① create_task 之前
② 协程体执行
③ 返回后 done = True
④ result = 42
```

删掉 `set_task_factory` 那行（默认惰性），同样逻辑必须改成：

```python
async def main():
    print("① create_task 之前")
    task = asyncio.create_task(coro())
    print("③ 返回后 done =", task.done())   # False
    print("④ result =", await task)          # 必须等待
```

输出顺序变为 `① → ③ → ② → ④`——**② 被"押后"到了事件循环轮转之后**。另外注意：惰性模式下 Task 未完成就调 `task.result()` 会抛 `InvalidStateError`，eager 且同步完成时则可以直接取。

## 如何启用（三种粒度）

```python
# ① 事件循环级：影响该 loop 上所有 create_task / TaskGroup.create_task
async def main():
    asyncio.get_running_loop().set_task_factory(asyncio.eager_task_factory)
    ...

# ② 单任务级（Python 3.14+）：eager_start 关键字参数
task = asyncio.create_task(coro(), eager_start=True)
# 不传 eager_start 时，跟随 loop 上 set_task_factory 设置的模式

# ③ 程序级：包装 loop_factory
def eager_loop():
    loop = asyncio.new_event_loop()
    loop.set_task_factory(asyncio.eager_task_factory)
    return loop

asyncio.run(main(), loop_factory=eager_loop)
```

两个容易踩的 API 事实：

- `asyncio.run()` 和 `asyncio.Runner` **没有** `task_factory` 参数（只有 `loop_factory`，3.12 加入），网上一些文章写的 `asyncio.run(main(), task_factory=...)` 是不存在的 API
- `TaskGroup` 内部通过 loop 创建子任务，工厂设置对结构化并发**同样生效**；Python 3.14 起 `TaskGroup.create_task()` 也直接支持 `eager_start=` 参数

## 姊妹 API：create_eager_task_factory()

如果需要**自定义 Task 子类**（埋点、追踪、instrumentation）同时保留 eager 行为，用它把两者组合起来：

```python
import asyncio

class TracedTask(asyncio.Task):
    ...  # 你的追踪逻辑

async def main():
    loop = asyncio.get_running_loop()
    loop.set_task_factory(asyncio.create_eager_task_factory(TracedTask))
```

要求传入的构造器签名兼容 `Task(loop, coro, **kwargs)` 并返回 Task 兼容对象。

## 性能：为什么快、快多少

- **官方数据**：What's New in Python 3.12 称该特性 "making some use-cases 2x to 5x faster"（部分场景 2~5 倍）
- **收益来源**：省掉 `call_soon` 入队 + 一整轮事件循环轮转。对**高频创建、且大多不真正阻塞**的短任务（缓存命中、纯内存计算、下游已就绪）收益最大；如果每个任务都要等真实 I/O，收益只剩"第一步提前"这一点
- **不是免费午餐**：急切执行的那段协程体现在 `create_task()` 的调用栈里。如果协程开头有一大段 CPU 密集代码，它现在会同步阻塞调用方——总计算量没变，只是发生的位置变了

## 陷阱清单

1. **"任务稍后才启动"的隐含假设失效**。大量代码依赖"create_task 之后协程还没跑"这一事实（例如先创建 task、再配置参数、再注册回调）。eager 下协程在你拿到返回值**之前**就开跑了——这正是官方警告"任务执行顺序可能改变"的根源。
2. **副作用时序前移**。日志、计数器、锁的获取顺序全部提前。比如协程开头 `async with lock`，无竞争时 eager 会当场拿到锁。
3. **`await task` 的"让出"行为改变**。惰性模式下刚创建的 task 一定未完成，`await` 必然让出一次控制权；eager 同步完成后 `await` 是同步返回的。如果代码靠 `await` 来"喘口气"避免饿死其他任务，行为会变。
4. **跨线程创建有已知问题**。在未运行目标 loop 的线程里用 eager 方式创建任务，Python 3.12/3.13 存在 current-task 追踪被破坏的问题，3.14 才加入防护。
5. **想跨版本统一使用**：可以参考 py-asynkit——eager 特性的原型库，提供向后兼容的 eager 工厂和 `@eager` 装饰器（按协程选择性急切执行）。

## 选型建议

**适合开启**：

- 高频、短生命周期的任务
- 任务大多能同步完成（缓存命中、下游就绪）
- 追求吞吐的 RPC / 代理 / 缓存类服务
- 偏好"拿到 handle 时任务已经跑起来"的语义（更接近 Trio nursery 的直觉）

**慎用或不开**：

- 存量代码大量依赖惰性时序
- 协程开头就有重计算
- 尚未做回归测试的项目

实践建议：**先在可测的子模块或单个 loop 上开启并做基准测试**，确认行为无回归后再扩大范围，而不是全局无脑替换。

## 参考

- [Python 官方文档：Coroutines and Tasks — Eager task factory](https://docs.python.org/3/library/asyncio-task.html#eager-task-factory)
- [What's New In Python 3.12](https://docs.python.org/3/whatsnew/3.12.html)
- [Meta Engineering：Python 3.12 新特性](https://engineering.fb.com/2023/10/05/developer-tools/python-312-meta-new-features/)
- [py-asynkit：eager 执行原型库](https://github.com/kristjanvalur/py-asynkit/)

---

上一章：[常用内置函数](./builtins.md) · [返回目录](../python.md)
