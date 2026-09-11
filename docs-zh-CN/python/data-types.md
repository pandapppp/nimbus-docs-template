---
title: 变量与数据类型
description: 复习 Python 的六种核心数据类型：数字、字符串、列表、元组、字典与集合。
sidebar:
  label: 变量与数据类型
  order: 40
---

Python 是**动态类型**语言，变量无需声明类型，赋值时自动推断。使用 `type()` 可查看变量的类型。

```python
x = 42              # 整数 int
y = 3.14            # 浮点数 float
name = "Alice"      # 字符串 str
is_ok = True        # 布尔值 bool

print(type(x))      # <class 'int'>

# 多重赋值
a, b, c = 1, 2, 3

# 交换变量（无需中间变量）
a, b = b, a
```

## 数字类型

| 类型 | 说明 | 示例 |
| --- | --- | --- |
| `int` | 整数（无大小限制） | `10, -5, 0, 999999999999` |
| `float` | 浮点数（64 位双精度） | `3.14, -0.001, 2e10` |
| `complex` | 复数 | `3+4j, 1j` |
| `bool` | 布尔值（int 的子类） | `True, False` |

```python
# 算术运算
print(7 / 2)     # 3.5  — 普通除法（返回 float）
print(7 // 2)    # 3    — 整除（向下取整）
print(7 % 2)     # 1    — 取余
print(2 ** 10)   # 1024 — 幂运算

# 类型转换
int("42")        # 42
float("3.14")    # 3.14
str(100)         # "100"
```

## 字符串（str）

字符串是**不可变**的字符序列，支持单引号、双引号和三引号。

```python
s1 = '单引号字符串'
s2 = "双引号字符串"
s3 = """三引号
可以跨行"""

# 索引与切片
name = "Python"
print(name[0])        # 'P' — 索引
print(name[0:3])      # 'Pyt' — 切片
print(name[-1])       # 'n' — 负索引
print(name * 3)       # 重复
print(name + "3.12")  # 拼接
print(len(name))      # 6 — 长度

# 常用方法
text = "  Hello, World  "
print(text.strip())               # 去两端空白
print(text.upper())               # 全大写
print(text.lower())               # 全小写
print(text.replace("World", "Python"))
print(text.split(","))            # 按分隔符拆分为列表
print(",".join(["a", "b", "c"]))  # 列表拼接为字符串
print("Hello".startswith("He"))   # True
print("Hello".find("l"))          # 2，找不到返回 -1
```

f-string 格式化（Python 3.6+）：

```python
name, age = "Alice", 30
print(f"我叫{name}，今年{age}岁")
print(f"{'居中对齐':^20}")   # 宽度 20 居中
print(f"{3.14159:.2f}")      # 3.14 — 保留两位小数
print(f"{1234567:,}")        # 1,234,567 — 千分位
```

## 列表（list）

列表是**有序、可变**的序列，用方括号 `[]` 表示，元素类型可以不同。

```python
fruits = ["apple", "banana", "cherry"]

# 索引与切片
print(fruits[0])       # 'apple'
print(fruits[-1])      # 'cherry'
print(fruits[1:])      # ['banana', 'cherry']

# 添加元素
fruits.append("date")           # 尾部添加
fruits.insert(0, "mango")       # 指定位置插入
fruits.extend(["kiwi", "lemon"])  # 批量扩展

# 删除元素
fruits.remove("banana")         # 按值删除
del fruits[0]                   # 按索引删除
popped = fruits.pop()           # 弹出最后一个

# 其他常用操作
print(len(fruits))              # 长度
print("apple" in fruits)        # 成员判断
fruits.sort()                   # 原地排序
fruits.reverse()                # 原地反转
sorted_list = sorted(fruits)    # 返回新列表，不改原列表
```

列表推导式（List Comprehension）是 Python 的标志性写法：

```python
squares = [x**2 for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
pairs = [(x, y) for x in range(3) for y in range(3) if x != y]
```

## 元组（tuple）

元组是**有序、不可变**的序列，用圆括号 `()` 表示。一旦创建，不能修改。

```python
point = (3, 4)
single = (42,)      # 单元素元组必须带逗号

# 解包
x, y = point
x, y, z = 1, 2, 3

# 不可变：point[0] = 10 会抛出 TypeError

# 元组方法
print(point.count(3))  # 元素出现次数
print(point.index(4))  # 元素首次出现的索引
```

## 字典（dict）

字典是**键值对**集合（Python 3.7+ 保持插入顺序），用花括号 `{}` 表示。键必须是不可变类型。

```python
person = {
    "name": "Alice",
    "age": 30,
    "city": "Beijing"
}

# 访问
print(person["name"])                  # 键不存在会抛 KeyError
print(person.get("email", "N/A"))      # 安全访问，可设默认值

# 修改与添加
person["age"] = 31                     # 修改
person["email"] = "a@mail.com"         # 添加

# 删除
del person["city"]
person.pop("email", None)              # 不存在时不报错

# 遍历
for key, value in person.items():
    print(f"{key}: {value}")
for key in person.keys():
    print(key)
for value in person.values():
    print(value)

# 字典推导式
squares = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

## 集合（set）

集合是**无序、不重复**的元素集合，用花括号 `{}` 或 `set()` 创建，支持集合运算。

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

print(a | b)   # 并集 {1, 2, 3, 4, 5, 6}
print(a & b)   # 交集 {3, 4}
print(a - b)   # 差集 {1, 2}
print(a ^ b)   # 对称差 {1, 2, 5, 6}

# 添加与删除
a.add(5)
a.remove(1)    # 不存在会报 KeyError
a.discard(99)  # 不存在不报错

# 列表去重的常用技巧
nums = [1, 2, 2, 3, 3, 3]
unique = list(set(nums))  # [1, 2, 3]
```

## 可变与不可变总结

| 类别 | 类型 | 特点 |
| --- | --- | --- |
| 不可变 | `int`, `float`, `str`, `tuple`, `bool` | 修改时会创建新对象 |
| 可变 | `list`, `dict`, `set` | 修改时原地改变，不创建新对象 |

> **陷阱**：可变对象作为函数默认参数是经典坑——默认参数只在函数定义时求值一次。应使用 `def f(items=None)` 然后在函数体内判断 `if items is None: items = []`。

---

上一章：[基本语法](./syntax.md) · 下一章：[运算符](./operators.md)
