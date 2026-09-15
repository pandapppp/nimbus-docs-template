---
title: LangChain 框架
description: 学习使用 LangChain 构建 Agent，掌握 Chain、Agent、Tool、Memory 等核心概念。
sidebar:
  label: LangChain 框架
  order: 40
---

**LangChain** 是最流行的 Agent 开发框架，提供了 Chain、Agent、Tool、Memory 等核心组件。

## 安装

```bash
pip install langchain langchain-openai langchain-community
```

## 核心组件

### 1. Model（模型）

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4",
    api_key="your-key",
    base_url="https://api.openai.com/v1",
    temperature=0.7
)

response = llm.invoke("你好")
print(response.content)
```

### 2. Prompt Template（提示模板）

```python
from langchain_core.prompts import ChatPromptTemplate

# 简单模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}，擅长{skill}"),
    ("user", "{input}")
])

# 格式化
messages = prompt.format_messages(
    role="Python 专家",
    skill="Web 开发",
    input="如何使用 FastAPI？"
)

response = llm.invoke(messages)
```

### 3. Chain（链）

```python
from langchain_core.output_parsers import StrOutputParser

# 创建链
chain = prompt | llm | StrOutputParser()

# 调用链
result = chain.invoke({
    "role": "Python 专家",
    "skill": "Web 开发",
    "input": "如何使用 FastAPI？"
})
print(result)
```

### 4. Tool（工具）

```python
from langchain_core.tools import tool

@tool
def get_weather(city: str) -> str:
    """获取指定城市的天气"""
    # 实际项目中调用天气 API
    return f"{city}今天25°C，晴朗"

@tool
def calculate(expression: str) -> str:
    """计算数学表达式"""
    return str(eval(expression))

# 工具列表
tools = [get_weather, calculate]
```

### 5. Agent（代理）

```python
from langchain_openai import ChatOpenAI
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain_core.prompts import ChatPromptTemplate

# 创建 LLM
llm = ChatOpenAI(model="gpt-4", api_key="your-key")

# 创建提示
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个有用的助手，可以使用工具来回答问题。"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}")
])

# 创建 Agent
agent = create_tool_calling_agent(llm, tools, prompt)

# 创建执行器
executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 运行
result = executor.invoke({"input": "北京天气怎么样？"})
print(result["output"])
```

## RAG（检索增强生成）

```python
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import FAISS
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough

# 1. 加载文档
loader = TextLoader("data.txt")
documents = loader.load()

# 2. 分割文本
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
docs = splitter.split_documents(documents)

# 3. 创建向量数据库
embeddings = OpenAIEmbeddings(api_key="your-key")
vectorstore = FAISS.from_documents(docs, embeddings)

# 4. 创建检索器
retriever = vectorstore.as_retriever()

# 5. 创建 RAG 链
prompt = ChatPromptTemplate.from_template("""
根据以下上下文回答问题：
{context}

问题：{question}
""")

chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

# 6. 提问
result = chain.invoke("什么是机器学习？")
print(result)
```

## Memory（记忆）

```python
from langchain_community.chat_message_histories import ChatMessageHistory
from langchain_core.chat_history import BaseChatMessageHistory
from langchain_core.runnables.history import RunnableWithMessageHistory

# 创建历史记录
store = {}

def get_session_history(session_id: str) -> BaseChatMessageHistory:
    if session_id not in store:
        store[session_id] = ChatMessageHistory()
    return store[session_id]

# 带记忆的链
chain_with_history = RunnableWithMessageHistory(
    chain,
    get_session_history,
    input_messages_key="input",
    history_messages_key="history"
)

# 对话
result = chain_with_history.invoke(
    {"input": "我叫张三"},
    config={"configurable": {"session_id": "123"}}
)

result = chain_with_history.invoke(
    {"input": "我叫什么名字？"},
    config={"configurable": {"session_id": "123"}}
)
print(result)  # 会记住你叫张三
```

## 结构化输出

```python
from pydantic import BaseModel, Field

class AnalysisResult(BaseModel):
    """分析结果"""
    summary: str = Field(description="总结")
    score: float = Field(description="评分 0-100")
    issues: list[str] = Field(description="问题列表")

# 结构化 LLM
structured_llm = llm.with_structured_output(AnalysisResult)

result = structured_llm.invoke("分析这段代码的质量...")
print(result.summary)
print(result.score)
print(result.issues)
```

## 最佳实践

| 实践 | 说明 |
| --- | --- |
| **使用 LCEL** | LangChain Expression Language，更简洁 |
| **工具描述清晰** | LLM 根据描述决定调用哪个工具 |
| **错误处理** | 工具调用失败要捕获异常 |
| **限制迭代次数** | 避免 Agent 陷入死循环 |
| **日志记录** | 开启 verbose 模式调试 |

掌握 LangChain 后，下一章学习[RAG 实战](/agent-dev/rag/)。
