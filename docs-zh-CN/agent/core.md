---
title: 02 核心层
description: Agent 开发的第二层：RAG 检索增强、函数调用/工具、记忆系统。让 Agent 具备记忆、检索与动手执行能力。
sidebar:
  label: 02 核心层
  order: 20
---

第一层让你能和模型对话。这一层让 Agent"**真的能干活**"：把私域知识塞给它（RAG）、让它能调用外部系统（函数调用）、让它记住上下文（记忆）。这三项是区分"聊天机器人"和"Agent"的分水岭。

## RAG 检索增强

RAG（Retrieval-Augmented Generation）把外部知识库的检索结果拼进 Prompt，让模型基于给定资料回答，解决"模型不知道、会编造"的问题。

### 要学什么

- **Embedding**：文本如何转成向量，如何度量相似度（余弦相似度）。
- **向量库**：为什么需要用向量数据库（而非普通数据库），主流选择（pgvector、Chroma、Milvus、FAISS、Qdrant）。
- **检索策略**：Top-K 取回、重排序（Rerank）、混合检索（关键词 + 向量）、分块（Chunk）策略。

### 学习文档

- [LangChain 官方 RAG 教程](https://python.langchain.com/docs/tutorials/rag/)：端到端构建一个 RAG 应用，实践性强。
- [向量数据库基础概念](https://python.langchain.com/docs/concepts/vectorstores/)：向量存储与相似度检索原理。
- [OpenAI Embeddings 文档](https://platform.openai.com/docs/guides/embeddings)：官方 Embedding 模型与用法。

## 函数调用 / 工具

函数调用（Tool Use / Function Calling）让模型在回答时"请求"调用某个函数，把真实系统的结果带回来：查数据库、发请求、执行计算，Agent 由此获得行动力。

### 要学什么

- **Tool 定义**：把函数描述成 JSON Schema（参数、必填、类型），让模型知道何时该调。
- **API 集成**：把工具接到真实接口，正确传参、解析返回。
- **错误处理**：工具失败时的降级、重试、把错误回传给模型让它换策略。

### 学习文档

- [OpenAI Function Calling 指南](https://platform.openai.com/docs/guides/function-calling)：官方工具调用实现与最佳实践。
- [Anthropic Tool Use 文档](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)：Claude 的工具调用官方教程。
- [LangChain Tools 概念](https://python.langchain.com/docs/concepts/tools/)：框架内工具抽象的用法。

## 记忆系统

记忆让 Agent 记得"你是异步上一个人"而没有记忆。记忆分为跨度不同的几层，工程上最关键的是状态管理。

### 要学什么

- **短期记忆**：把当前对话历史放进上下文，处理超标的裁剪与摘要。
- **长期记忆**：把重要信息写入存储（数据库、向量库、内存），跨会话复用。
- **状态管理**：会话状态如何持久化、恢复、并发隔离，避免脏读与串话。

### 学习文档

- [LangChain 记忆概念](https://python.langchain.com/docs/concepts/memory/)：框架内记忆类型的抽象与选择。
- [LangGraph State 概念](https://langchain-ai.github.io/langgraph/concepts/low_level/)：图状态管理，Agent 状态持久化的核心。
- [链推理的 MemGPT / Letta](https://github.com/letta-ai/letta)（Letta 原 MemGPT）：受 OS 虚拟内存启发的长期记忆系统，进阶参考。

---
上一页：[01 基础层](./basics.md) · [返回技能树](../agent.md) · 下一页：[03 框架层](./framework.md)