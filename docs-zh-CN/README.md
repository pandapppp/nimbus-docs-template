# Nimbus Docs Template

<img src="./assets/nimbus-mark.svg" alt="Nimbus 官方 Logo" width="160" />

这个站点将 GitHub 仓库中的 Markdown 发布为文档，提供导航、搜索和主题切换。当前发布的是 `docs-zh-CN/` 目录中的中文文档。

## Python 基础复习

一套系统的 Python 复习文档，共 13 章，覆盖从环境搭建、核心语法到面向对象与 asyncio 进阶内容：

- [Python 简介与特点](./python/introduction.md)：语言历史、核心特性与应用领域。
- [变量与数据类型](./python/data-types.md)：数字、字符串、列表、元组、字典、集合。
- [函数](./python/functions.md)：参数、lambda、作用域与 LEGB 规则。
- [面向对象编程基础](./python/oop.md)：类、继承、多态与魔术方法。
- [asyncio.eager_task_factory 详解](./python/async-eager-tasks.md)：Python 3.12 急切任务执行进阶专题。

完整章节见 [Python 基础复习目录](./python.md)。

## Agent 开发技能树

从基础到进阶的四层学习路径，系统覆盖构建 AI Agent 所需的全部知识点，含"要学什么"与"学习文档"：

- [01 基础层](./agent/basics.md)：Python 编程、LLM 原理、Prompt 工程。
- [02 核心层](./agent/core.md)：RAG 检索增强、函数调用 / 工具、记忆系统。
- [03 框架层](./agent/framework.md)：LangChain、Agent 框架、MCP 协议。
- [04 进阶层](./agent/advanced.md)：多 Agent 协作、评估与可观测、部署与运维。

完整目录见 [Agent 开发技能树总览](./agent.md)。

## 内容版本

每次构建都会读取指定分支上的一个确定提交。页脚和 `/_build.json` 显示该文档提交的 SHA，可用来核对线上内容的版本。修改文档并推送后，重新触发构建即可发布更新。

文档源维护在 `docs-zh-CN/` 目录中；`src/content/docs/`、`public/_source/` 和 `dist/` 是构建生成目录。