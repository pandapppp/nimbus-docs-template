---
title: 01 基础层
description: Agent 开发的第一层：Python 编程、LLM 原理、Prompt 工程。掌握与模型对话的最小工具箱。
sidebar:
  label: 01 基础层
  order: 10
---

第一层解决"**用什么和模型对话**"。这一层不直接写 Agent，而是打好三个基础：把 Python 用到顺手、搞懂 LLM 到底能做什么、学会用 Prompt 驱动模型。这三样是后面所有 Agent 能力的底座。

## Python 编程

写 Agent 需要大量调用异步 API、集成外部服务。这一主题要学到"能独立完成一个带网络请求和类型约束的脚本"。

### 要学什么

- **异步编程**：`async` / `await`、事件循环、`asyncio.create_task`、`TaskGroup`，会用并发同时请求多个模型或工具。
- **类型标注**：`typing` 模块、`Optional` / `Union` / `dict` 泛型、`dataclass`，让代码可维护、可被 IDE 智能提示。
- **HTTP 调用**：`httpx` / `requests` 的 GET/POST、请求头、超时、重试、流式响应（SSE / stream）。

### 学习文档

- [Python 官方教程](https://docs.python.org/3/tutorial/)：权威入门，先读函数、模块、类三章。
- [asyncio 官方文档](https://docs.python.org/3/library/asyncio.html)：异步核心语法与 API。
- [typing 官方文档](https://docs.python.org/3/library/typing.html)：类型标注标准库。
- [httpx 官方文档](https://www.python-httpx.org/)：现代 HTTP 客户端，支持异步与流式。

## LLM 原理

不需要推导数学公式，但要建立对模型的"心理模型"，知道它强在哪、弱在哪，才能合理设置边界。

### 要学什么

- **模型能力**：GPT / Claude / 开源模型（Qwen、Llama）各自擅长什么，多模态、推理、指令遵循的区别。
- **Token 机制**：文本如何被拆成 Token，计费与速度怎么算，为什么长文本贵、延迟高。
- **上下文窗口**：输入 + 输出长度限制，超窗会截断或报错，如何裁剪与滑动窗口。

### 学习文档

- [Hugging Face 官方文档](https://huggingface.co/docs)：模型、Tokenizer、推理的通用入门，含免费试用。
- [Transformer 架构图解](https://jalammar.github.io/illustrated-transformer/)：最经典的直观讲解。
- [OpenAI Tokenizer 工具](https://platform.openai.com/tokenizer)：可视化 Token 划分，直观理解计数方式。
- [每门语言的 Attention 论文精读](https://arxiv.org/abs/1706.03762)："Attention Is All You Need"，进阶必读。

## Prompt 工程

Prompt 是写给模型的指令，决定它出什么质量的答案。这是投入产出比最高的一项技能。

### 要学什么

- **结构化提示**：用清晰的系统提示 + 角色设定 + 任务描述 + 输出格式约束。
- **少样本（Few-shot）**：在 Prompt 里给 2~3 个输入输出示例，让模型模仿格式与规则。
- **思维链（Chain-of-Thought）**：引导模型"先推理再回答"，显著提升复杂问题的正确率。

### 学习文档

- [OpenAI Prompt Engineering 指南](https://platform.openai.com/docs/guides/prompt-engineering)：官方最佳实践，结构化提示与常用技巧。
- [提示工程指南](https://www.promptingguide.ai/)：覆盖多种模型与进阶技法（CoT、ToT、Self-Consistency）。
- [思维链论文](https://arxiv.org/abs/2201.11903)：作者原版 Few-shot CoT 论文。

---
上一页：[返回技能树](../agent.md) · 下一页：[02 核心层](./core.md)