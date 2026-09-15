---
title: 缓存和队列
description: 学习使用 Redis 缓存和消息队列提升后端性能。
sidebar:
  label: 缓存和队列
  order: 40
---

**Redis** 是最常用的缓存和消息队列工具，可以大幅提升后端性能。

## 安装

```bash
pip install redis
```

## 基础操作

```python
import redis

# 连接 Redis
r = redis.Redis(host='localhost', port=6379, db=0, decode_responses=True)

# 字符串操作
r.set('name', '张三')
print(r.get('name'))  # 张三

# 设置过期时间（秒）
r.setex('token', 3600, 'abc123')

# 自增
r.incr('counter')  # 1
r.incr('counter')  # 2

# 列表
r.lpush('queue', 'task1', 'task2')
r.rpop('queue')  # task1

# 集合
r.sadd('tags', 'python', 'fastapi')
r.smembers('tags')  # {'python', 'fastapi'}

# 哈希
r.hset('user:1', mapping={'name': '张三', 'age': 25})
r.hget('user:1', 'name')  # 张三
```

## FastAPI 缓存

```python
from fastapi import FastAPI
import redis
import json

app = FastAPI()
r = redis.Redis(host='localhost', port=6379, db=0, decode_responses=True)

@app.get("/users/{user_id}")
def get_user(user_id: int):
    # 先查缓存
    cache_key = f"user:{user_id}"
    cached = r.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # 缓存没有，查数据库
    user = db.query(User).filter(User.id == user_id).first()
    
    # 写入缓存，过期时间 5 分钟
    r.setex(cache_key, 300, json.dumps(user.to_dict()))
    
    return user.to_dict()
```

## 缓存装饰器

```python
from functools import wraps
import json

def cache(expire_seconds: int = 300):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            # 生成缓存键
            cache_key = f"{func.__name__}:{str(args)}:{str(kwargs)}"
            
            # 查缓存
            cached = r.get(cache_key)
            if cached:
                return json.loads(cached)
            
            # 执行函数
            result = func(*args, **kwargs)
            
            # 写缓存
            r.setex(cache_key, expire_seconds, json.dumps(result))
            
            return result
        return wrapper
    return decorator

# 使用
@cache(expire_seconds=600)
def get_user(user_id: int):
    return db.query(User).filter(User.id == user_id).first().to_dict()
```

## 消息队列

### 简单任务队列

```python
import redis
import json

r = redis.Redis(host='localhost', port=6379, db=0, decode_responses=True)

# 生产者：添加任务
def add_task(task_type: str, data: dict):
    task = json.dumps({"type": task_type, "data": data})
    r.lpush("task_queue", task)

# 消费者：处理任务
def process_tasks():
    while True:
        task_data = r.brpop("task_queue")
        task = json.loads(task_data[1])
        
        if task["type"] == "send_email":
            send_email(task["data"])
        elif task["type"] == "generate_report":
            generate_report(task["data"])
```

### 使用 Celery

```bash
pip install celery[redis]
```

```python
# celery_app.py
from celery import Celery

app = Celery('tasks', broker='redis://localhost:6379/0')

@app.task
def send_email(to: str, subject: str, body: str):
    # 发送邮件逻辑
    print(f"发送邮件到 {to}")

# 使用
from celery_app import send_email

# 异步调用
send_email.delay("user@example.com", "欢迎", "你好")
```

## 常见缓存策略

| 策略 | 说明 | 适用场景 |
| --- | --- | --- |
| **Cache-Aside** | 先查缓存，没有查数据库 | 通用 |
| **Write-Through** | 写数据库同时写缓存 | 数据一致性要求高 |
| **Write-Behind** | 先写缓存，异步写数据库 | 写入频繁 |
| **TTL** | 设置过期时间 | 数据有时效性 |

## 缓存穿透/击穿/雪崩

| 问题 | 说明 | 解决方案 |
| --- | --- | --- |
| **穿透** | 查询不存在的数据 | 布隆过滤器、缓存空值 |
| **击穿** | 热点 key 过期 | 互斥锁、永不过期 |
| **雪崩** | 大量 key 同时过期 | 随机过期时间 |

掌握缓存和队列后，下一章学习[Docker 部署](/backend/docker/)。
