---
title: FastAPI 入门
description: 学习 FastAPI 框架基础，快速构建高性能的 Python Web API。
sidebar:
  label: FastAPI 入门
  order: 10
---

**FastAPI** 是 Python 中最快的 Web 框架之一，基于类型提示自动生成 API 文档，非常适合构建后端 API。

## 安装

```bash
pip install fastapi uvicorn
```

## 第一个 API

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
```

运行服务：

```bash
uvicorn main:app --reload
```

访问 http://localhost:8000 查看 API。

## 路由

```python
@app.get("/users")        # GET 请求
@app.post("/users")       # POST 请求
@app.put("/users/{id}")   # PUT 请求
@app.delete("/users/{id}") # DELETE 请求
@app.patch("/users/{id}") # PATCH 请求
```

## 请求参数

### 路径参数

```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}
```

### 查询参数

```python
@app.get("/users")
def list_users(skip: int = 0, limit: int = 10, search: str = None):
    return {"skip": skip, "limit": limit, "search": search}
```

### 请求体

```python
from pydantic import BaseModel

class UserCreate(BaseModel):
    name: str
    email: str
    age: int = None

@app.post("/users")
def create_user(user: UserCreate):
    return {"name": user.name, "email": user.email}
```

### 表单数据

```python
from fastapi import Form

@app.post("/login")
def login(username: str = Form(...), password: str = Form(...)):
    return {"username": username}
```

### 文件上传

```python
from fastapi import File, UploadFile

@app.post("/upload")
def upload_file(file: UploadFile = File(...)):
    return {"filename": file.filename}
```

## 响应模型

```python
from pydantic import BaseModel

class UserResponse(BaseModel):
    id: int
    name: str
    email: str

@app.get("/users/{user_id}", response_model=UserResponse)
def get_user(user_id: int):
    return {"id": user_id, "name": "张三", "email": "zhangsan@example.com"}
```

## 错误处理

```python
from fastapi import HTTPException

@app.get("/users/{user_id}")
def get_user(user_id: int):
    if user_id == 0:
        raise HTTPException(status_code=404, detail="用户不存在")
    return {"user_id": user_id}
```

## 依赖注入

```python
from fastapi import Depends

def get_db():
    db = DatabaseSession()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def list_users(db = Depends(get_db)):
    return db.query(User).all()
```

## 中间件

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## API 文档

FastAPI 自动生成 API 文档：

- **Swagger UI**：http://localhost:8000/docs
- **ReDoc**：http://localhost:8000/redoc

## 项目结构

```
project/
├── main.py           # 入口
├── routers/          # 路由模块
│   ├── users.py
│   └── items.py
├── models/           # 数据模型
│   ├── user.py
│   └── item.py
├── schemas/          # Pydantic 模型
│   ├── user.py
│   └── item.py
├── services/         # 业务逻辑
│   └── user.py
├── database.py       # 数据库连接
├── config.py         # 配置
└── requirements.txt
```

掌握 FastAPI 基础后，下一章学习[数据库操作](/backend/database/)。
