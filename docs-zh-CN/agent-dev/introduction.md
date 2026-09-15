---
title: Agent 开发概述
description: 了解 AI Agent 的核心概念、技术栈和发展趋势。
sidebar:
  label: Agent 开发概述
  order: 10
---

**AI Agent** 是能自主感知环境、做出决策并执行行动的智能体。不同于简单的聊天机器人，Agent 具备**工具调用、记忆、规划**等能力。

## 什么是 Agent？

| 概念 | 说明 | 类比 |
| --- | --- | --- |
| **Chatbot** | 对话机器人，只回答问题 | 客服热线 |
| **Copilot** | 辅助工具，协助人类工作 | 副驾驶 |
| **Agent** | 自主执行任务，可调用工具 | 真人助理 |

### Agent 的核心能力

| 能力 | 说明 |
| --- | --- |
| **推理** | 理解任务、分析问题、制定计划 |
| **工具调用** | 使用 API、执行代码、操作数据库 |
| **记忆** | 短期记忆（对话上下文）和长期记忆（持久化） |
| **规划** | 分解任务、制定执行步骤 |
| **反思** | 评估结果、调整策略 |

## 技术栈总览

```
┌─────────────────────────────────────────┐
│            Agent 应用层                   │
│  (对话助手、自动化、数据分析、代码生成)      │
├─────────────────────────────────────────┤
│            Agent 框架层                   │
│  (LangChain、LlamaIndex、AutoGen)        │
├─────────────────────────────────────────┤
│            AI 模型层                     │
│  (OpenAI、Claude、DeepSeek、本地模型)      │
├─────────────────────────────────────────┤
│            基础设施层                     │
│  (向量数据库、缓存、消息队列、部署)         │
└─────────────────────────────────────────┘
```

## 主流框架对比

| 框架 | 优势 | 劣势 | 适用场景 |
| --- | --- | --- | --- |
| **LangChain** | 生态完善、功能全面 | 学习曲线陡、版本迭代快 | 通用 Agent |
| **LlamaIndex** | RAG 专精、索引能力强 | Agent 能力较弱 | 知识库 Agent |
| **AutoGen** | 多 Agent 协作 | 配置复杂 | 多 Agent 系统 |
| **CrewAI** | 角色定义直观 | 社区较小 | 团队协作 Agent |
| **直接调 API** | 灵活、无依赖 | 需要自己造轮子 | 轻量级 Agent |

## LLM API 基础

所有 Agent 的核心都是 LLM API。最常用的是 **OpenAI 兼容协议**：

```python
import openai

client = openai.OpenAI(
    api_key="your-api-key",
    base_url="https://api.openai.com/v1"  # 或其他兼容服务
)

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "你是一个有用的助手"},
        {"role": "user", "content": "你好"}
    ],
    temperature=0.7,
    max_tokens=1000
)

print(response.choices[0].message.content)
```

### 主流 LLM 服务

| 服务 | 模型 | 特点 |
| --- | --- | --- |
| **OpenAI** | GPT-4o、GPT-4.1 | 能力最强、价格较高 |
| **Anthropic** | Claude 4 Opus/Sonnet | 长上下文、安全 |
| **DeepSeek** | DeepSeek V4 | 性价比高、中文好 |
| **Google** | Gemini 2.5 | 多模态、长上下文 |
| **本地模型** | Llama、Qwen | 免费、隐私 |

## Function Calling（工具调用）

Function Calling 是 Agent 调用外部工具的核心机制：

```python
# 定义工具
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取指定城市的天气",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "城市名称"
                    }
                },
                "required": ["city"]
            }
        }
    }
]

# 调用 LLM
response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "北京天气怎么样？"}],
    tools=tools,
    tool_choice="auto"
)

# 处理工具调用
tool_call = response.choices[0].message.tool_calls[0]
print(f"调用工具: {tool_call.function.name}")
print(f"参数: {tool_call.function.arguments}")
```

## 学习路线

1. **基础**：LLM API、Prompt Engineering
2. **核心**：Function Calling、LangChain
3. **进阶**：RAG、Memory、Multi-Agent
4. **实战**：项目实战、部署上线

准备好了吗？下一章学习[Prompt Engineering](/agent-dev/prompt-engineering/)。
