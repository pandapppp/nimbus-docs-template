---
title: 常用内置函数
description: 汇总 print、len、range、enumerate、zip、map、filter、sorted 等高频内置函数及组合用法。
sidebar:
  label: 常用内置函数
  order: 120
---

Python 提供了 70+ 个内置函数，无需导入即可直接使用。以下按功能分组复习最常用的部分。

## 速查表

| 函数 | 说明 | 示例 |
| --- | --- | --- |
| `print()` | 输出文本 | `print("hello")` |
| `input()` | 读取用户输入（返回 str） | `name = input()` |
| `len()` | 返回长度 | `len([1,2,3])` → `3` |
| `type()` | 返回类型 | `type(42)` → `<class 'int'>` |
| `isinstance()` | 判断类型（支持继承） | `isinstance(42, int)` → `True` |
| `range()` | 生成数字序列 | `list(range(5))` → `[0,1,2,3,4]` |
| `enumerate()` | 返回索引 + 元素 | `list(enumerate(['a','b']))` |
| `zip()` | 并行打包多个序列 | `list(zip([1,2],['a','b']))` |
| `map()` | 对每个元素应用函数 | `list(map(str, [1,2]))` |
| `filter()` | 过滤元素 | `list(filter(bool, [0,1,2]))` |
| `sorted()` | 排序（返回新列表） | `sorted([3,1,2])` → `[1,2,3]` |
| `reversed()` | 反转序列 | `list(reversed([1,2,3]))` |
| `sum()` | 求和 | `sum([1,2,3])` → `6` |
| `max()` / `min()` | 最大值 / 最小值 | `max([3,1,2])` → `3` |
| `abs()` | 绝对值 | `abs(-5)` → `5` |
| `round()` | 四舍五入 | `round(3.14159, 2)` → `3.14` |
| `int()` / `float()` / `str()` / `bool()` | 类型转换 | `int("42")` → `42` |
| `list()` / `tuple()` / `dict()` / `set()` | 创建容器 | `list("abc")` → `['a','b','c']` |
| `any()` / `all()` | 任一为真 / 全部为真 | `any([False, True])` → `True` |
| `divmod()` | 同时取商和余 | `divmod(17, 5)` → `(3, 2)` |
| `dir()` | 查看对象属性 | `dir(str)` |
| `help()` | 查看帮助文档 | `help(print)` |
| `open()` | 打开文件 | `open("file.txt", "r")` |
| `id()` | 对象内存地址 | `id(x)` |
| `eval()` | 执行字符串表达式 | `eval("1+2")` → `3` |

## 组合用法实战

### enumerate + zip 组合

```python
names = ["Alice", "Bob", "Charlie"]
scores = [85, 92, 78]
for i, (name, score) in enumerate(zip(names, scores), start=1):
    print(f"{i}. {name}: {score}分")
```

### sorted + key + lambda

```python
people = [
    {"name": "Alice", "age": 30},
    {"name": "Bob", "age": 25},
]
by_age = sorted(people, key=lambda p: p["age"])
by_name_desc = sorted(people, key=lambda p: p["name"], reverse=True)
```

### map + filter 链式调用

```python
# 0~9 中偶数的平方
result = list(
    map(lambda x: x ** 2,
        filter(lambda x: x % 2 == 0, range(10)))
)
# [0, 4, 16, 36, 64]
```

Python 3.5+ 也可以用生成器表达式获得同样结果，可读性往往更好：

```python
result = [x ** 2 for x in range(10) if x % 2 == 0]
```

### any / all 判断

```python
nums = [0, 1, 2]
print(any(n > 1 for n in nums))   # True — 至少一个满足
print(all(n > 0 for n in nums))   # False — 0 不满足
```

### divmod 商余同时取

```python
q, r = divmod(17, 5)   # q=3, r=2
```

## 探索更多

```python
# 查看全部内置名称
print(dir(__builtins__))

# 查看某个函数的文档
help(sorted)
```

完整列表参考官方文档 [Built-in Functions](https://docs.python.org/3/library/functions.html)。

---

上一章：[面向对象编程基础](./oop.md) · 下一章：[asyncio.eager_task_factory 详解](./async-eager-tasks.md)
