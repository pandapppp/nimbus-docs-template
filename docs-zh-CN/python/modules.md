---
title: 模块与包
description: 复习模块的多种导入方式、包的结构，以及 __name__ 与 __main__ 的作用。
sidebar:
  label: 模块与包
  order: 80
---

模块是代码组织的基本单位，包是模块的目录层级结构。

## 模块

模块就是一个 `.py` 文件，包含函数、类、变量的定义。假设有 `math_utils.py`：

```python
# math_utils.py
def add(a, b):
    return a + b

def multiply(a, b):
    return a * b

PI = 3.14159
```

在其他文件中导入：

```python
# 方式 1：导入整个模块
import math_utils
print(math_utils.add(2, 3))

# 方式 2：导入特定名称
from math_utils import add, multiply
print(add(2, 3))

# 方式 3：导入并设置别名
import math_utils as mu
print(mu.multiply(2, 3))

# 方式 4：导入全部（不推荐，容易命名冲突）
from math_utils import *
```

### 常用标准库

```python
import os          # 操作系统接口
import sys         # 系统相关参数
import datetime    # 日期时间
import random      # 随机数
import json        # JSON 处理
import math        # 数学函数
import re          # 正则表达式
import pathlib     # 面向对象的路径操作
```

## 包

包是包含多个模块的**目录**，目录下有一个 `__init__.py` 文件（可以为空），用于标识该目录是一个 Python 包：

```text
my_project/
├── main.py
└── my_package/
    ├── __init__.py
    ├── module_a.py
    └── sub_package/
        ├── __init__.py
        └── module_b.py
```

从包中导入：

```python
import my_package.module_a
from my_package import module_a
from my_package.sub_package import module_b
```

## `__name__` 与 `"__main__"`

每个模块都有一个 `__name__` 属性：

- 模块被**直接运行**时，`__name__` 等于 `"__main__"`
- 模块被**导入**时，`__name__` 等于模块名

这构成了 Python 的入口文件惯用写法：

```python
def main():
    print("程序主逻辑")

if __name__ == "__main__":
    # 只有直接运行此文件时才执行，被 import 时不执行
    main()
```

## 第三方库的安装与使用

```bash
# 安装
pip install requests
```

```python
import requests

resp = requests.get("https://api.github.com")
print(resp.status_code)
```

依赖管理的细节见[安装与环境配置](./setup.md)。

---

上一章：[函数](./functions.md) · 下一章：[文件操作](./files.md)
