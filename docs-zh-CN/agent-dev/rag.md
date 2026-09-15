---
title: RAG 实战
description: 掌握检索增强生成(RAG)技术，构建知识库问答 Agent。
sidebar:
  label: RAG 实战
  order: 50
---

**RAG（Retrieval-Augmented Generation）** 是让 LLM 基于外部知识回答问题的技术，是知识库 Agent 的核心。

## RAG 流程

```
用户提问 → 文本向量化 → 相似度检索 → 拼接上下文 → LLM 生成回答
```

| 步骤 | 说明 | 技术 |
| --- | --- | --- |
| 文档加载 | 读取 PDF、Word、网页等 | LangChain Loaders |
| 文本分割 | 切成小块 | Text Splitters |
| 向量化 | 文本转为向量 | Embedding 模型 |
| 存储 | 存入向量数据库 | FAISS、Chroma |
| 检索 | 相似度搜索 | 向量搜索 |
| 生成 | LLM 基于上下文回答 | ChatGPT 等 |

## 完整示例

```python
from langchain_community.document_loaders import (
    TextLoader, PyPDFLoader, DirectoryLoader
)
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_community.vectorstores import FAISS
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough
from langchain_core.output_parsers import StrOutputParser

# 1. 加载文档
loader = DirectoryLoader("./docs", glob="**/*.txt", loader_cls=TextLoader)
documents = loader.load()

# 2. 分割文本
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,      # 每块 500 字符
    chunk_overlap=50,    # 重叠 50 字符
    separators=["\n\n", "\n", "。", "，", " "]
)
docs = splitter.split_documents(documents)

# 3. 创建向量数据库
embeddings = OpenAIEmbeddings(api_key="your-key")
vectorstore = FAISS.from_documents(docs, embeddings)

# 4. 保存/加载
vectorstore.save_local("./faiss_index")
# vectorstore = FAISS.load_local("./faiss_index", embeddings)

# 5. 创建检索器
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 3}  # 返回最相关的 3 个文档
)

# 6. 创建 RAG 链
prompt = ChatPromptTemplate.from_template("""
基于以下上下文回答问题。如果上下文中没有相关信息，请说明"根据已有知识无法回答"。

上下文：
{context}

问题：{question}

回答：
""")

llm = ChatOpenAI(model="gpt-4", api_key="your-key")

chain = (
    {"context": retriever, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

# 7. 提问
answer = chain.invoke("什么是机器学习？")
print(answer)
```

## 文档加载器

```python
# 文本文件
from langchain_community.document_loaders import TextLoader
loader = TextLoader("data.txt")

# PDF 文件
from langchain_community.document_loaders import PyPDFLoader
loader = PyPDFLoader("document.pdf")

# CSV 文件
from langchain_community.document_loaders import CSVLoader
loader = CSVLoader("data.csv")

# 网页
from langchain_community.document_loaders import WebBaseLoader
loader = WebBaseLoader("https://example.com")

# Markdown
from langchain_community.document_loaders import UnstructuredMarkdownLoader
loader = UnstructuredMarkdownLoader("README.md")

# 目录批量加载
from langchain_community.document_loaders import DirectoryLoader
loader = DirectoryLoader("./docs", glob="**/*.md")
```

## 文本分割策略

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

# 基本分割
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    separators=["\n\n", "\n", "。", "，", " "]
)

# 按字符数分割
splitter = CharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    separator="\n"
)

# 按 token 数分割
splitter = RecursiveCharacterTextSplitter.from_tiktoken_encoder(
    chunk_size=500,
    chunk_overlap=50
)
```

## 向量数据库对比

| 数据库 | 特点 | 适用场景 |
| --- | --- | --- |
| **FAISS** | 本地、快速、免费 | 开发测试 |
| **Chroma** | 简单易用、持久化 | 小型项目 |
| **Pinecone** | 云托管、可扩展 | 生产环境 |
| **Weaviate** | 功能丰富、支持混合搜索 | 复杂需求 |
| **Milvus** | 高性能、分布式 | 大规模数据 |

## 检索优化

### 混合检索

```python
# 结合关键词和向量检索
from langchain.retrievers import EnsembleRetriever
from langchain_community.retrievers import BM25Retriever

# BM25 关键词检索
bm25_retriever = BM25Retriever.from_documents(docs)
bm25_retriever.k = 3

# 向量检索
vector_retriever = vectorstore.as_retriever(search_kwargs={"k": 3})

# 混合检索
ensemble_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, vector_retriever],
    weights=[0.4, 0.6]
)
```

### 重排序

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain_cohere import CohereRerank

# 重排序器
reranker = CohereRerank(model="rerank-v3.5", top_n=3)

# 带重排序的检索器
retriever = ContextualCompressionRetriever(
    base_compressor=reranker,
    base_retriever=vectorstore.as_retriever(search_kwargs={"k": 10})
)
```

## 常见问题

| 问题 | 解决方案 |
| --- | --- |
| 回答不准确 | 增加 chunk_overlap、调整 k 值 |
| 检索不到相关内容 | 优化文档分割、使用混合检索 |
| 回答太长 | 在 prompt 中限制回答长度 |
| 回答编造信息 | 在 prompt 中强调"基于上下文" |

掌握 RAG 后，下一章学习[部署上线](/agent-dev/deployment/)。
