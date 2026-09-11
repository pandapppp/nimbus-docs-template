---
title: 04 进阶层
description: Agent 开发的第四层：多 Agent 协作、评估与可观测、部署与运维。把单 Agent 扩展到系统级工程。
sidebar:
  label: 04 进阶层
  order: 40
---

前三层让你能做出"能跑"的 Agent。进阶层解决"**怎么跑稳、跑聪明、跑得便宜**"，把它从 Demo 变成可上线的系统级工程。核心是协作、可观测、和成本安全的平衡。

## 多 Agent 协作

单个 Agent 有能力和上下文上限，多 Agent 通过分工配合完成更复杂的任务——但也引入新的协调问题。

### 要学什么

- **任务分解**：把大任务拆成可独立执行的子任务（Plan → 派发 → 汇总）。
- **角色分工**：给每个 Agent 明确职责（检索、推理、执行、审校），避免互相干扰。
- **通信**：Agent 之间如何传递结果与状态（消息、共享内存、图内状态），以及并发与死锁。

### 学习文档

- [LangGraph 多 Agent 概念](https://langchain-ai.github.io/langgraph/concepts/multi_agent/)：图驱动的多 Agent 架构模式。
- [CrewAI 协作机制](https://docs.crewai.com/concepts/crews)：任务分解与角色协作的声明式玩法。
- [基于图的 Agent 模式综述](https://arxiv.org/abs/2407.10654)（AgentSymbolicLearn 综述等）：多 Agent 模式的系统梳理，进阶参考。

## 评估与可观测

LLM 输出是概率性的，不做评估和观测就无法迭代，也无法定位线上问题。

### 要学什么

- **评估（Evaluation）**：LLM-as-a-judge、基准集（golden set）、准确率 / 忠实度 / 相关性指标、回归测试。
- **Tracing**：记录每次调用的完整链路（Prompt、模型、工具、中间结果）。
- **监控调试**：Latency、错误率、Token 消耗的可视化，快速定位是模型问题还是工具问题。

### 学习文档

- [LangSmith 官方文档](https://docs.smith.langchain.com/)：评估与 Tracing 的一体化平台（企业常用）。
- [Langfuse 官方文档](https://langfuse.com/docs)：开源 LLM 可观测平台，支持自托管。
- [OpenTelemetry LLM 规范](https://opentelemetry.io/docs/specs/semconv/)：可观测性的开放标准，进阶必读。

## 部署与运维

把 Agent 推到生产环境，代码只是第一步，真正的成本在运维：控制费用、保证稳定、守住安全边界。

### 要学什么

- **成本优化**：模型选型（便宜模型做简单事）、缓存、批量与降级、Token 用量审计。
- **限流与弹性**：Rate Limit、请求排队、超时与重试、并发控制，避免烧钱或被封。
- **安全护栏**：Prompt 注入防护、输出内容过滤、敏感信息脱敏、权限最小化、审计日志。

### 学习文档

- [Cloudflare Workers AI](https://developers.cloudflare.com/workers-ai/)：Serverless 部署 LLM 应用的边缘平台。
- [Anthropic 安全与稳健性指南](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/security)（及官方 Robustness 手册）：生产级安全与提示注入防护。
- [OpenAI 生产最佳实践](https://platform.openai.com/docs/guides/your-prompts)：部署、缓存、限流与成本的企业实践。

---
上一页：[03 框架层](./framework.md) · [返回技能树](../agent.md)