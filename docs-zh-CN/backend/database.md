---
title: 数据库操作
description: 学习使用 SQLAlchemy ORM 操作 MySQL/PostgreSQL 数据库。
sidebar:
  label: 数据库操作
  order: 20
---

数据库是后端开发的核心。使用 **SQLAlchemy** ORM 可以用 Python 代码操作数据库，不用写 SQL。

## 安装

```bash
pip install sqlalchemy
# MySQL
pip install pymysql
# PostgreSQL
pip install psycopg2-binary
```

## 连接数据库

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase

# SQLite
engine = create_engine("sqlite:///./data.db")

# MySQL
engine = create_engine("mysql+pymysql://user:password@localhost:3306/dbname")

# PostgreSQL
engine = create_engine("postgresql://user:password@localhost:5432/dbname")

# 创建会话
SessionLocal = sessionmaker(bind=engine)

# 基类
class Base(DeclarativeBase):
    pass
```

## 定义模型

```python
from sqlalchemy import Column, Integer, String, DateTime, ForeignKey, Boolean
from sqlalchemy.orm import relationship
from datetime import datetime

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(50), nullable=False)
    email = Column(String(100), unique=True, index=True)
    age = Column(Integer, default=0)
    is_active = Column(Boolean, default=True)
    created_at = Column(DateTime, default=datetime.now)
    
    # 关系
    posts = relationship("Post", back_populates="author")

class Post(Base):
    __tablename__ = "posts"
    
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(200), nullable=False)
    content = Column(String(1000))
    user_id = Column(Integer, ForeignKey("users.id"))
    
    author = relationship("User", back_populates="posts")
```

## 创建表

```python
# 创建所有表
Base.metadata.create_all(engine)
```

## CRUD 操作

### 创建

```python
db = SessionLocal()

# 创建用户
user = User(name="张三", email="zhangsan@example.com", age=25)
db.add(user)
db.commit()
db.refresh(user)  # 获取生成的 ID
print(user.id)
```

### 查询

```python
# 查询所有
users = db.query(User).all()

# 条件查询
user = db.query(User).filter(User.name == "张三").first()

# 多条件查询
users = db.query(User).filter(
    User.age >= 18,
    User.is_active == True
).all()

# 排序
users = db.query(User).order_by(User.created_at.desc()).all()

# 分页
users = db.query(User).offset(0).limit(10).all()

# 计数
count = db.query(User).count()

# 模糊查询
users = db.query(User).filter(User.name.like("%张%")).all()
```

### 更新

```python
# 方法一：直接修改
user = db.query(User).filter(User.id == 1).first()
user.name = "李四"
db.commit()

# 方法二：批量更新
db.query(User).filter(User.age < 18).update({"is_active": False})
db.commit()
```

### 删除

```python
# 删除单条
user = db.query(User).filter(User.id == 1).first()
db.delete(user)
db.commit()

# 批量删除
db.query(User).filter(User.is_active == False).delete()
db.commit()
```

## 关系查询

```python
# 一对多查询
user = db.query(User).filter(User.id == 1).first()
print(user.posts)  # 获取用户的所有文章

# 联表查询
results = db.query(User, Post).join(Post).all()
for user, post in results:
    print(f"{user.name}: {post.title}")
```

## 事务

```python
try:
    user = User(name="新用户", email="new@example.com")
    db.add(user)
    db.commit()
except Exception as e:
    db.rollback()
    raise e
finally:
    db.close()
```

## 数据库迁移（Alembic）

```bash
pip install alembic
alembic init alembic
alembic revision --autogenerate -m "create users table"
alembic upgrade head
```

## 与 FastAPI 集成

```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def list_users(db: Session = Depends(get_db)):
    return db.query(User).all()

@app.post("/users")
def create_user(user_data: UserCreate, db: Session = Depends(get_db)):
    user = User(**user_data.dict())
    db.add(user)
    db.commit()
    db.refresh(user)
    return user
```

掌握数据库操作后，下一章学习[认证授权](/backend/auth/)。
