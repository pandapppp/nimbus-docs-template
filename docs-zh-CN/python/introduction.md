---
title: Python 简介与特点
description: 了解 Python 的历史背景、核心设计哲学、语言特点与主要应用领域。
sidebar:
  label: Python 简介与特点
  order: 10
---

Python 是一种**高级、通用、解释型**的编程语言，由 Guido van Rossum 于 1989 年圣诞节期间开始设计，1991 年首次发布。Python 的设计哲学强调代码的**可读性**和**简洁性**，其语法允许程序员用比 C++ 或 Java 更少的代码行表达复杂的逻辑。

## 核心特点

| 特点 | 说明 |
| --- | --- |
| 简洁优雅 | 语法清晰，接近自然语言，代码量通常仅为 C/Java 的 1/3 到 1/5 |
| 开源免费 | 遵循 PSF 许可证，社区活跃，第三方库极其丰富 |
| 跨平台 | 支持 Windows、macOS、Linux 等主流操作系统，一次编写到处运行 |
| 多范式 | 支持面向对象、函数式、过程式编程，灵活适应不同场景 |
| 动态类型 | 变量无需声明类型，运行时自动推断，开发效率高 |
| 自动内存管理 | 内置垃圾回收机制，无需手动分配和释放内存 |

## 应用领域

Python 广泛应用于以下领域，是当今最受欢迎的编程语言之一：

- **Web 开发**：Django、Flask、FastAPI
- **数据科学与分析**：NumPy、Pandas、Matplotlib
- **人工智能与机器学习**：TensorFlow、PyTorch、scikit-learn
- **自动化运维与脚本**：Ansible、Fabric、各类批处理脚本
- **网络爬虫**：Requests、Scrapy、BeautifulSoup
- **金融量化分析**：QuantLib、Backtrader、Zipline

## Python 之禅

在 Python 交互环境中输入 `import this` 可以看到 Python 的设计哲学——"The Zen of Python"。其中最著名的几条：

> Simple is better than complex.（简洁胜于复杂）
>
> Readability counts.（可读性很重要）
>
> There should be one—and preferably only one—obvious way to do it.（做一件事最好只有一种显而易见的方法）

这些原则渗透在 Python 的语法设计中，也是编写"Pythonic"代码的指导思想。

## 版本说明

目前主流版本是 Python 3.x（Python 2 已于 2020 年停止维护）。本套文档基于 Python 3.8+ 编写，涉及新版本特性时会单独标注，例如[asyncio.eager_task_factory](./async-eager-tasks.md) 需要 Python 3.12+。

---

下一章：[安装与环境配置](./setup.md)
