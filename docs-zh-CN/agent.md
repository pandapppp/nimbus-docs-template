---
title: Agent 开发技能树
description: 从基础到进阶的四层学习路径，覆盖 Python、LLM 原理、Prompt 工程、RAG、函数调用、记忆、LangChain、Agent 框架、MCP、多 Agent 协作、评估与部署运维。
sidebar:
  order: 70
  group:
    label: Agent 开发技能树
    hideIndex: true
---

这套技能树是把"如何从零开始构建 AI Agent"拆成**四个递进的学习层**。每一层解决一类问题：基础层回答"用什么对话"，核心层回答"Agent 凭什么自己干活"，框架层回答"怎么快速搭建"，进阶层回答"怎么跑稳、跑聪明、跑得便宜"。

建议按序学习：前面的层是后一层的前置知识，跳过容易卡壳。

## 四层结构

| 层级 | 主题 | 目的 |
| --- | --- | --- |
| [01 基础层](./agent/basics.md) | Python 编程、LLM 原理、Prompt 工程 | 掌握与模型对话所需的最小工具箱 |
| [02 核心层](./agent/core.md) | RAG、函数调用 / 工具、记忆系统 | 让 Agent 具备记忆、检索与动手执行能力 |
| [03 框架层](./agent/framework.md) | LangChain、Agent 框架、MCP 协议 | 用成熟框架和标准协议快速组装 |
| [04 进阶层](./agent/advanced.md) | 多 Agent 协作、评估可观测、部署运维 | 把单 Agent 扩展到系统级工程 |

## 对应关系

每层之下都有"要学什么"和"学习文档"两份清单：

- **要学什么**：该主题必须掌握的核心知识点，用里程碑衡量。
- **学习文档**：可循序学习的官方文档与优质参考资料链接。

```text
01 基础层
├── Python 编程          → 异步、类型标注、HTTP 调用
├── LLM 原理             → 模型能力、Token、上下文窗口
└── Prompt 工程          → 结构化提示、少样本、思维链

02 核心层
├── RAG 检索增强         → 向量库、Embedding、检索策略
├── 函数调用 / 工具      → Tool Use、API 集成、错误处理
└── 记忆系统            → 短期/长期记忆、状态管理

03 框架层
├── LangChain            → 链式编排、组件复用
├── Agent 框架           → ReAct、Plan-Execute、CrewAI
└── MCP 协议            → 工具标准化、跨模型互联

04 进阶层
├── 多 Agent 协作        → 任务分解、角色分工、通信
├── 评估与可观测         → 测试、Tracing、监控调试
└── 部署与运维          → 成本优化、限流、安全护栏
```

每一层的完整知识点与参考文档见对应页面：

- [基础层：Python、LLM、Prompt](./agent/basics.md)
- [核心层：RAG、工具、记忆](./agent/core.md)
- [框架层：LangChain、Agent、MCP](./agent/framework.md)
- [进阶层：多 Agent、评估、部署](./agent/advanced.md)