---
title: 认证授权
description: 学习 JWT 认证、权限控制和安全防护。
sidebar:
  label: 认证授权
  order: 30
---

认证（你是谁）和授权（你能做什么）是后端安全的核心。

## JWT 认证

### 安装

```bash
pip install python-jose[cryptography] passlib[bcrypt]
```

### 生成 Token

```python
from datetime import datetime, timedelta
from jose import jwt

SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

def create_access_token(data: dict, expires_delta: timedelta = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=15))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

# 使用
token = create_access_token(
    data={"sub": "user@example.com"},
    expires_delta=timedelta(hours=24)
)
```

### 验证 Token

```python
from jose import JWTError, jwt

def verify_token(token: str):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return payload
    except JWTError:
        return None
```

## FastAPI 认证实现

### 完整示例

```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from passlib.context import CryptContext
from jose import JWTError, jwt
from datetime import datetime, timedelta
from pydantic import BaseModel

app = FastAPI()

# 密码哈希
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

# OAuth2 方案
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# 配置
SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"

# 用户模型
class User(BaseModel):
    username: str
    email: str
    disabled: bool = False

# 模拟数据库
fake_users_db = {
    "admin": {
        "username": "admin",
        "email": "admin@example.com",
        "hashed_password": pwd_context.hash("admin123")
    }
}

# 密码验证
def verify_password(plain_password, hashed_password):
    return pwd_context.verify(plain_password, hashed_password)

# 获取用户
def get_user(username: str):
    if username in fake_users_db:
        return fake_users_db[username]
    return None

# 认证用户
def authenticate_user(username: str, password: str):
    user = get_user(username)
    if not user or not verify_password(password, user["hashed_password"]):
        return None
    return user

# 获取当前用户
async def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="认证失败",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
    except JWTError:
        raise credentials_exception
    
    user = get_user(username)
    if user is None:
        raise credentials_exception
    return user

# 登录接口
@app.post("/token")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="用户名或密码错误",
        )
    access_token = create_access_token(
        data={"sub": user["username"]},
        expires_delta=timedelta(hours=24)
    )
    return {"access_token": access_token, "token_type": "bearer"}

# 受保护的接口
@app.get("/users/me")
async def read_users_me(current_user: dict = Depends(get_current_user)):
    return current_user
```

## 权限控制

### 基于角色的权限（RBAC）

```python
from enum import Enum

class Role(str, Enum):
    ADMIN = "admin"
    USER = "user"
    GUEST = "guest"

# 角色检查
def require_role(roles: list[Role]):
    async def role_checker(current_user: dict = Depends(get_current_user)):
        if current_user.get("role") not in roles:
            raise HTTPException(
                status_code=status.HTTP_403_FORBIDDEN,
                detail="权限不足"
            )
        return current_user
    return role_checker

# 使用
@app.get("/admin/users")
async def admin_users(current_user: dict = Depends(require_role([Role.ADMIN]))):
    return {"message": "管理员页面"}
```

## 安全最佳实践

| 实践 | 说明 |
| --- | --- |
| **密码哈希** | 使用 bcrypt，不要明文存储 |
| **HTTPS** | 生产环境必须使用 |
| **Token 过期** | 设置合理的过期时间 |
| **刷新 Token** | 使用 refresh_token 续期 |
| **限流** | 防止暴力破解 |
| **CORS** | 限制允许的域名 |

掌握认证授权后，下一章学习[缓存和队列](/backend/cache/)。
