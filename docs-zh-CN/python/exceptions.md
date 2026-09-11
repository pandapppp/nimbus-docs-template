---
title: 异常处理
description: 复习 try/except/else/finally 结构、raise 抛出、自定义异常与常见内置异常。
sidebar:
  label: 异常处理
  order: 100
---

异常是程序运行时发生的错误。Python 使用 `try/except` 捕获和处理异常，避免程序崩溃。

## 基本结构

```python
try:
    num = int(input("输入数字: "))
    result = 10 / num
    print(f"结果: {result}")
except ValueError:
    print("输入不是有效数字")
except ZeroDivisionError:
    print("不能除以零")
except Exception as e:
    print(f"其他错误: {e}")
else:
    print("没有异常时执行")
finally:
    print("无论是否异常都会执行")
```

四个子句的分工：

| 子句 | 执行时机 |
| --- | --- |
| `try` | 包裹可能出错的代码 |
| `except` | 捕获指定异常后执行 |
| `else` | try 块**没有**发生异常时执行 |
| `finally` | 无论如何都执行，常用于资源清理 |

## 主动抛出异常

```python
def set_age(age):
    if age < 0:
        raise ValueError("年龄不能为负数")
    return age

try:
    set_age(-5)
except ValueError as e:
    print(e)  # 年龄不能为负数
```

## 自定义异常

继承 `Exception` 即可定义业务异常：

```python
class MyCustomError(Exception):
    """自定义异常类"""
    def __init__(self, message, code=0):
        super().__init__(message)
        self.code = code

try:
    raise MyCustomError("操作失败", code=500)
except MyCustomError as e:
    print(f"错误: {e}, 代码: {e.code}")
```

## 常见内置异常

| 异常 | 说明 |
| --- | --- |
| `SyntaxError` | 语法错误 |
| `NameError` | 变量名未定义 |
| `TypeError` | 类型错误 |
| `ValueError` | 值错误（如 `int("abc")`） |
| `IndexError` | 索引越界 |
| `KeyError` | 字典键不存在 |
| `AttributeError` | 属性不存在 |
| `ZeroDivisionError` | 除以零 |
| `FileNotFoundError` | 文件未找到 |
| `ImportError` | 导入模块失败 |

## 异常链与最佳实践

```python
# 用 raise ... from 保留原始异常上下文
try:
    data = json.loads(raw)
except json.JSONDecodeError as e:
    raise ValueError("配置文件格式错误") from e
```

最佳实践要点：

- **精确捕获**：优先捕获具体异常，避免裸 `except:` 吞掉所有错误（包括拼写错误和 Ctrl-C）
- **不要用异常做流程控制**：异常处理有性能成本，语义上应留给"异常"情况
- **清理资源用 finally 或 with**：见[文件操作](./files.md)
- EAFP：Python 文化推崇"先斩后奏"（Easier to Ask Forgiveness than Permission）——直接尝试操作，失败再处理，而不是事先反复检查

---

上一章：[文件操作](./files.md) · 下一章：[面向对象编程基础](./oop.md)
