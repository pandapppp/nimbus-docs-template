---
title: 基本语法
description: 掌握 Python 的缩进规则、注释写法、续行符与基本的输入输出。
sidebar:
  label: 基本语法
  order: 30
---

本章覆盖 Python 最基础的语法规则：第一个程序、缩进与代码块、注释、续行，以及输入输出。

## 第一个程序

按照传统，从 "Hello World" 开始：

```python
print("Hello, World!")
```

## 缩进与代码块

Python 使用**缩进**标识代码块，而不是像 C/Java 那样使用大括号 `{}`。通常使用 4 个空格作为一个缩进层级。

```python
if 10 > 5:
    print("10 大于 5")      # 缩进 4 格
    print("属于同一个代码块")
print("代码块结束")          # 无缩进，回到上层
```

> **注意**：同一个代码块中的缩进必须保持一致，混用空格和 Tab 会导致 `IndentationError`。推荐在编辑器中设置 Tab = 4 空格。

## 注释

Python 支持**单行注释**和**多行注释**：

```python
# 这是一个单行注释

"""
这是一个多行注释（文档字符串）
可以写多行内容
通常用于函数、类、模块的说明文档
"""

# 单独成行的多行字符串，效果同多行注释
```

## 行与续行

Python 中一条语句通常占一行。如需换行续写，使用反斜杠 `\` 或括号内自然换行：

```python
# 反斜杠续行
total = 1 + \
        2 + \
        3

# 括号内自然换行（推荐）
names = ["Alice",
         "Bob",
         "Charlie"]

# 一行多条语句（用分号分隔，不推荐）
a = 1; b = 2; c = 3
```

## 输入与输出

```python
# 输出：print() 函数
print("Hello")
print("姓名:", name, "年龄:", age)          # 多参数以空格分隔
print(f"我叫{name}，今年{age}岁")           # f-string，推荐写法

# 输入：input() 函数（返回值始终是字符串）
name = input("请输入你的名字: ")
age = int(input("请输入你的年龄: "))         # 需要数字时手动转换
```

f-string（Python 3.6+）是最推荐的字符串格式化方式，更多用法见[变量与数据类型](./data-types.md#字符串-str)。

---

上一章：[安装与环境配置](./setup.md) · 下一章：[变量与数据类型](./data-types.md)
