---
title: 部署上线
description: 学习将 Agent 部署为 API 服务，掌握 FastAPI、Docker 和流式输出。
sidebar:
  label: 部署上线
  order: 60
---

Agent 开发完成后，需要部署为 API 服务才能被其他人使用。

## FastAPI 部署

### 基础服务

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from langchain_openai import ChatOpenAI
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate

app = FastAPI()

# 初始化 LLM
llm = ChatOpenAI(model="gpt-4", api_key="your-key")
tools = [...]  # 你的工具列表
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个有用的助手"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}")
])

agent = create_tool_calling_agent(llm, tools, prompt)
executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

class ChatRequest(BaseModel):
    message: str
    session_id: str = "default"

@app.post("/chat")
async def chat(request: ChatRequest):
    try:
        result = executor.invoke({"input": request.message})
        return {"response": result["output"]}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

### 流式输出

```python
from fastapi.responses import StreamingResponse
from langchain_core.messages import HumanMessage

@app.post("/chat/stream")
async def chat_stream(request: ChatRequest):
    async def generate():
        async for event in executor.astream_events(
            {"input": request.message},
            version="v2"
        ):
            if event["event"] == "on_chat_model_stream":
                content = event["data"]["chunk"].content
                if content:
                    yield f"data: {content}\n\n"
    
    return StreamingResponse(generate(), media_type="text/event-stream")
```

### SSE 客户端

```python
import requests

response = requests.post(
    "http://localhost:8000/chat/stream",
    json={"message": "你好"},
    stream=True
)

for line in response.iter_lines():
    if line:
        print(line.decode("utf-8").removeprefix("data: "))
```

## Docker 部署

### Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### requirements.txt

```
fastapi==0.109.0
uvicorn==0.27.0
langchain==0.3.0
langchain-openai==0.2.0
langchain-community==0.3.0
python-dotenv==1.0.0
```

### docker-compose.yml

```yaml
version: '3.8'
services:
  agent:
    build: .
    ports:
      - "8000:8000"
    environment:
      - OPENAI_API_KEY=your-key
    volumes:
      - ./data:/app/data
    restart: unless-stopped
```

## 环境变量管理

```python
# .env 文件
OPENAI_API_KEY=your-key
DATABASE_URL=sqlite:///./data.db
REDIS_URL=redis://localhost:6379

# 代码中加载
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.getenv("OPENAI_API_KEY")
```

## 监控和日志

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

@app.post("/chat")
async def chat(request: ChatRequest):
    logger.info(f"收到请求: {request.message[:50]}...")
    try:
        result = executor.invoke({"input": request.message})
        logger.info(f"回答长度: {len(result['output'])}")
        return {"response": result["output"]}
    except Exception as e:
        logger.error(f"错误: {str(e)}")
        raise HTTPException(status_code=500, detail=str(e))
```

## 成本优化

| 策略 | 说明 |
| --- | --- |
| **模型选择** | 简单任务用小模型，复杂任务用大模型 |
| **缓存** | 相同问题缓存回答 |
| **限制 Token** | 设置 max_tokens |
| **批量处理** | 合并多个请求 |
| **异步处理** | 非阻塞调用 |

恭喜！你已经掌握了 Agent 开发的核心技能。回到[Agent 开发首页](/agent-dev/)查看完整教程。
