---
title: 03 框架层
description: Agent 开发的第三层：LangChain、Agent 框架、MCP 协议。用成熟框架和标准协议快速组装 Agent。
sidebar:
  label: 03 框架层
  order: 30
---

前两层让你掌握了 RAG、工具、记忆这些"零件"。这一层把它们**组装成可用的 Agent**，并学习业界标准（MCP），避免自己从零造轮子、避免被单一框架锁死。

## LangChain

LangChain 是生态最成熟的 Agent 框架，提供链式编排和大量可复用组件，大幅降低集成成本。

### 要学什么

- **链式编排**：`Chain` 把多步骤串联（Prompt → 模型 → 解析 → 再 Prompt），`LCEL` 语法组织流水线。
- **组件复用**：模型封装、Prompt 模板、Output Parser、Retriever、Tool、Memory 的开箱即用封装。
- **LangGraph**：LangChain 进阶的图驱动运行时，支持循环、分支与状态机（实际 Agent 更新的核心）。

### 学习文档

- [LangChain 官方文档](https://python.langchain.com/docs/)：入门从"Quick start"和"Tutorials"开始。
- [LangSmith 文档](https://docs.smith.langchain.com/)：配套的追踪、评估与调试平台。
- [LangGraph 官方文档](https://langchain-ai.github.io/langgraph/)：Agent 图运行时，进阶层必学。

## Agent 框架

理解 Agent 的几种"大脑循环"范式，再落到具体框架（CrewAI 等）快速产出可用的多 Agent 系统。

### 要学什么

- **ReAct**：Reasoning + Acting 循环，模型"想一步 → 调一个工具 → 看结果 → 再想"。
- **Plan-Execute**：先规划成子任务，再依次执行，适合步骤明确的复杂任务。
- **CrewAI**：以"角色"组织多 Agent（如 分析师、写手、审校），声明式配置协作。

### 学习文档

- [ReAct 论文](https://arxiv.org/abs/2210.03629)：Agent 范式奠基论文，必读。
- [CrewAI 官方文档](https://docs.crewai.com/)：多 Agent 角色协作的低门槛框架。
- [LangGraph Agent 教程](https://langchain-ai.github.io/langgraph/tutorials/introduction/)：如何在框架内实现 ReAct / 工具循环。

## MCP 协议

MCP（Model Context Protocol）是 Anthropic 提出的开放协议，把"模型↔工具/数据"的连接方式标准化，让同一套工具能被不同模型、不同客户端复用。

### 要学什么

- **工具标准化**：如何把一个能力暴露成 MCP Server（tool、resource、prompt 三要素）。
- **客户端接入**：模型 / Agent 如何通过 MCP Client 发现并调用远端工具。
- **跨模型互联**：写好一次 MCP Server，换模型、换前端都能用，打破单框架锁定。

### 学习文档

- [MCP 官方文档](https://modelcontextprotocol.io/)：协议规范、快速入门与 SDK。
- [MCP 规范详解](https://modelcontextprotocol.io/specification/)：客户端 / 服务端 / 工具的定义细节。
- [MCP 生态示例仓库](https://github.com/modelcontextprotocol/servers)：大量现成 Server 可参考。

---
上一页：[02 核心层](./core.md) · [返回技能树](../agent.md) · 下一页：[04 进阶层](./advanced.md)