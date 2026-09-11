---
title: Python 基础复习
description: 系统复习 Python 核心语法与进阶特性，从简介与安装到面向对象和 asyncio 急切任务执行。
sidebar:
  order: 60
  group:
    label: Python 基础复习
    hideIndex: true
---

这套文档用于系统复习 Python 基础知识，共 13 章。前 12 章覆盖环境配置、核心语法、数据结构、函数、模块、文件、异常处理、面向对象和常用内置函数；最后一章是进阶专题，深入讲解 Python 3.12 引入的 `asyncio.eager_task_factory`。

## 学习路径

- **基础入门**：[Python 简介与特点](./python/introduction.md) → [安装与环境配置](./python/setup.md) → [基本语法](./python/syntax.md)
- **核心语法**：[变量与数据类型](./python/data-types.md) → [运算符](./python/operators.md) → [控制流](./python/control-flow.md)
- **代码组织**：[函数](./python/functions.md) → [模块与包](./python/modules.md) → [文件操作](./python/files.md) → [异常处理](./python/exceptions.md)
- **进阶提升**：[面向对象编程基础](./python/oop.md) → [常用内置函数](./python/builtins.md) → [asyncio.eager_task_factory 详解](./python/async-eager-tasks.md)

## 章节一览

| 章节 | 内容要点 |
| --- | --- |
| [简介与特点](./python/introduction.md) | 语言历史、核心特性、应用领域 |
| [安装与环境配置](./python/setup.md) | 解释器安装、pip、虚拟环境 |
| [基本语法](./python/syntax.md) | 缩进、注释、续行、输入输出 |
| [变量与数据类型](./python/data-types.md) | int/float/str/list/tuple/dict/set |
| [运算符](./python/operators.md) | 算术、比较、逻辑、赋值、成员、身份 |
| [控制流](./python/control-flow.md) | if/elif/else、for/while、break/continue |
| [函数](./python/functions.md) | 参数、lambda、作用域与 LEGB |
| [模块与包](./python/modules.md) | import、`__init__.py`、`__name__` |
| [文件操作](./python/files.md) | open/with、读写模式、os 与 pathlib |
| [异常处理](./python/exceptions.md) | try/except、raise、自定义异常 |
| [面向对象](./python/oop.md) | 类、继承、多态、魔术方法 |
| [内置函数](./python/builtins.md) | print/len/range/map/filter/sorted 等 |
| [eager_task_factory 详解](./python/async-eager-tasks.md) | 急切任务执行、性能与陷阱 |

每章末尾提供上一章 / 下一章导航，也可以直接从左侧侧栏进入任意章节。
