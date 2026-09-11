---
title: 函数
description: 复习函数定义、各类参数、lambda 表达式、作用域与 LEGB 查找规则。
sidebar:
  label: 函数
  order: 70
---

函数是**可复用的代码块**，使用 `def` 关键字定义。

## 函数定义与调用

```python
def greet(name):
    """打招呼的函数（文档字符串）"""
    return f"Hello, {name}!"

message = greet("Alice")
print(message)  # Hello, Alice!

# 无返回值函数，隐式返回 None
def print_info(info):
    print(info)
```

## 参数

### 默认参数与关键字参数

```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Alice")                       # Hello, Alice!
greet("Bob", "Hi")                   # Hi, Bob!
greet(name="Charlie", greeting="Hey")  # 关键字参数，与顺序无关
```

### 可变参数 *args 与 **kwargs

```python
# *args — 可变位置参数，收集为元组
def sum_all(*args):
    return sum(args)

print(sum_all(1, 2, 3, 4, 5))  # 15

# **kwargs — 可变关键字参数，收集为字典
def print_info(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=30, city="Beijing")

# 混合使用：顺序为 位置参数 → 默认参数 → *args → **kwargs
def func(a, b, *args, **kwargs):
    print(a, b, args, kwargs)

func(1, 2, 3, 4, x=10, y=20)
# 1 2 (3, 4) {'x': 10, 'y': 20}
```

### 解包传参

`*` 和 `**` 在调用侧做解包：

```python
args = (1, 2, 3)
kwargs = {"x": 10, "y": 20}
func(*args, **kwargs)  # 等价于 func(1, 2, 3, x=10, y=20)
```

## 返回值

```python
# 多返回值（实际上是返回元组）
def divide_and_mod(a, b):
    return a // b, a % b

quotient, remainder = divide_and_mod(17, 5)
print(quotient, remainder)  # 3 2
```

## Lambda 表达式

Lambda 是**匿名函数**，适用于简单的、一次性的函数定义，常与 `map` / `filter` / `sorted` 搭配：

```python
square = lambda x: x ** 2
print(square(5))  # 25

# map — 对每个元素应用函数
nums = [3, 1, 4, 1, 5, 9]
squared = list(map(lambda x: x**2, nums))
# [9, 1, 16, 1, 25, 81]

# filter — 过滤元素
evens = list(filter(lambda x: x % 2 == 0, nums))
# [4]

# sorted — 自定义排序键
students = [("Alice", 85), ("Bob", 92), ("Charlie", 78)]
by_score = sorted(students, key=lambda s: s[1], reverse=True)
# [('Bob', 92), ('Alice', 85), ('Charlie', 78)]
```

更多内置函数的用法见[常用内置函数](./builtins.md)。

## 变量作用域

```python
x = "全局变量"

def test_scope():
    x = "局部变量"     # 创建局部变量，不影响全局
    print(x)          # 局部变量

test_scope()
print(x)              # 全局变量

def modify_global():
    global x          # 声明使用全局变量
    x = "被修改了"

modify_global()
print(x)              # 被修改了
```

### LEGB 查找规则

Python 按以下顺序查找变量名：

1. **L**ocal — 函数内部
2. **E**nclosing — 嵌套函数的外层函数
3. **G**lobal — 模块级别
4. **B**uilt-in — 内置名称

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        x = "local"
        print(x)      # local
    inner()

outer()
```

在嵌套函数中修改外层变量使用 `nonlocal` 关键字（类似 `global`，但作用于 Enclosing 层）。

---

上一章：[控制流](./control-flow.md) · 下一章：[模块与包](./modules.md)
