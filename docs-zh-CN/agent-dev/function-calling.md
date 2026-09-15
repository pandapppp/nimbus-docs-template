---
title: Function Calling
description: 掌握 Function Calling 机制，让 Agent 能够调用外部工具和 API。
sidebar:
  label: Function Calling
  order: 30
---

**Function Calling** 是 Agent 调用外部工具的核心机制。通过它，LLM 可以决定何时调用什么工具，并传入正确参数。

## 工作流程

```
用户提问 → LLM 分析 → 决定调用工具 → 返回工具调用请求
→ 执行工具 → 将结果返回 LLM → LLM 生成最终回答
```

## 基础示例

```python
import openai
import json

client = openai.OpenAI(api_key="your-key")

# 1. 定义工具
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "获取指定城市的当前天气",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "城市名称，如：北京、上海"
                    },
                    "unit": {
                        "type": "string",
                        "enum": ["celsius", "fahrenheit"],
                        "description": "温度单位"
                    }
                },
                "required": ["city"]
            }
        }
    }
]

# 2. 定义工具实现
def get_weather(city: str, unit: str = "celsius") -> dict:
    # 实际项目中这里调用天气 API
    return {
        "city": city,
        "temperature": 25,
        "unit": unit,
        "condition": "晴朗"
    }

# 3. 对话循环
messages = [{"role": "user", "content": "北京天气怎么样？"}]

response = client.chat.completions.create(
    model="gpt-4",
    messages=messages,
    tools=tools,
    tool_choice="auto"
)

# 4. 处理工具调用
message = response.choices[0].message
if message.tool_calls:
    tool_call = message.tool_calls[0]
    func_name = tool_call.function.name
    func_args = json.loads(tool_call.function.arguments)
    
    # 执行工具
    if func_name == "get_weather":
        result = get_weather(**func_args)
    
    # 将结果返回 LLM
    messages.append(message)
    messages.append({
        "role": "tool",
        "tool_call_id": tool_call.id,
        "content": json.dumps(result, ensure_ascii=False)
    })
    
    # LLM 生成最终回答
    final_response = client.chat.completions.create(
        model="gpt-4",
        messages=messages
    )
    print(final_response.choices[0].message.content)
```

## 多工具定义

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "search_database",
            "description": "搜索数据库中的记录",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string", "description": "搜索关键词"},
                    "table": {"type": "string", "description": "表名"},
                    "limit": {"type": "integer", "description": "返回数量"}
                },
                "required": ["query", "table"]
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "send_email",
            "description": "发送邮件",
            "parameters": {
                "type": "object",
                "properties": {
                    "to": {"type": "string", "description": "收件人邮箱"},
                    "subject": {"type": "string", "description": "邮件主题"},
                    "body": {"type": "string", "description": "邮件内容"}
                },
                "required": ["to", "subject", "body"]
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "execute_sql",
            "description": "执行 SQL 查询",
            "parameters": {
                "type": "object",
                "properties": {
                    "sql": {"type": "string", "description": "SQL 语句"}
                },
                "required": ["sql"]
            }
        }
    }
]
```

## 并行工具调用

有些情况下 LLM 会同时调用多个工具：

```python
response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "查一下北京和上海的天气"}],
    tools=tools,
    tool_choice="auto"
)

message = response.choices[0].message
if message.tool_calls:
    results = []
    for tool_call in message.tool_calls:
        func_name = tool_call.function.name
        func_args = json.loads(tool_call.function.arguments)
        result = get_weather(**func_args)
        results.append({
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": json.dumps(result, ensure_ascii=False)
        })
    
    messages.append(message)
    messages.extend(results)
```

## 工具注册模式

```python
class ToolRegistry:
    def __init__(self):
        self.tools = {}
        self.schemas = []
    
    def register(self, name: str, description: str, parameters: dict):
        def decorator(func):
            self.tools[name] = func
            self.schemas.append({
                "type": "function",
                "function": {
                    "name": name,
                    "description": description,
                    "parameters": parameters
                }
            })
            return func
        return decorator
    
    def execute(self, name: str, arguments: dict):
        return self.tools[name](**arguments)

registry = ToolRegistry()

@registry.register(
    name="get_weather",
    description="获取天气",
    parameters={
        "type": "object",
        "properties": {
            "city": {"type": "string"}
        },
        "required": ["city"]
    }
)
def get_weather(city: str):
    return {"city": city, "temp": 25}
```

## 错误处理

```python
def safe_execute_tool(func_name: str, args: dict, tools: dict) -> str:
    try:
        result = tools[func_name](**args)
        return json.dumps({"success": True, "data": result}, ensure_ascii=False)
    except Exception as e:
        return json.dumps({"success": False, "error": str(e)}, ensure_ascii=False)
```

## 最佳实践

| 实践 | 说明 |
| --- | --- |
| **工具描述清晰** | LLM 根据描述决定是否调用 |
| **参数类型明确** | 使用 JSON Schema 定义 |
| **错误处理完善** | 工具执行失败要返回错误信息 |
| **结果简洁** | 返回必要的信息，不要太多 |
| **幂等性** | 工具应该是幂等的，避免副作用 |

掌握 Function Calling 后，下一章学习[LangChain 框架](/agent-dev/langchain/)。
