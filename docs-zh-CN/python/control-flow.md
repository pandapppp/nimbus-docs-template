---
title: 控制流
description: 复习条件语句、for 与 while 循环，以及 break、continue、pass 和循环 else 子句。
sidebar:
  label: 控制流
  order: 60
---

控制流决定程序的执行路径。本章复习条件语句和循环语句两大类。

## 条件语句

Python 使用 `if` / `elif` / `else` 进行条件判断：

```python
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
elif score >= 60:
    grade = "D"
else:
    grade = "F"

print(f"等级: {grade}")  # B

# 多条件组合
age = 25
has_license = True
if age >= 18 and has_license:
    print("可以开车")

# 成员判断直接做条件
fruits = ["apple", "banana"]
if "apple" in fruits:
    print("有苹果")
```

条件表达式（三元运算符）：

```python
status = "及格" if score >= 60 else "不及格"
```

## for 循环

for 循环用于遍历任何可迭代对象：

```python
# 遍历列表
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# 遍历字符串
for ch in "abc":
    print(ch)
```

### range() 生成数字序列

```python
for i in range(5):         # 0, 1, 2, 3, 4
    print(i)

for i in range(2, 8):      # 2, 3, 4, 5, 6, 7
    print(i)

for i in range(0, 10, 2):  # 0, 2, 4, 6, 8（步长 2）
    print(i)
```

### enumerate() 获取索引

```python
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# 可指定起始编号
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}. {fruit}")
```

### zip() 并行遍历

```python
names = ["Alice", "Bob"]
ages = [25, 30]
for name, age in zip(names, ages):
    print(f"{name} {age}岁")
```

## while 循环

while 循环在条件为真时反复执行：

```python
count = 0
while count < 5:
    print(f"count = {count}")
    count += 1
```

> **注意**：务必确保循环条件最终会变为假，否则造成死循环。

## break、continue 与 pass

```python
# break — 立即跳出整个循环
for num in range(100):
    if num == 5:
        break
    print(num)  # 输出 0~4

# continue — 跳过本次迭代，进入下一次
for num in range(10):
    if num % 2 == 0:
        continue
    print(num)  # 输出奇数 1, 3, 5, 7, 9

# pass — 空操作占位符，保持结构完整
for num in range(5):
    pass  # 什么都不做
```

## 循环 else 子句

循环可以带 `else`，仅在循环**没有被 break 中断**、正常结束时执行：

```python
# 查找示例：找到就 break，else 不执行
for item in [1, 3, 5]:
    if item % 2 == 0:
        print(f"找到偶数 {item}")
        break
else:
    print("没有偶数")  # 本例输出这行
```

这个特性适合"查找失败"场景，代替额外的标志变量。

---

上一章：[运算符](./operators.md) · 下一章：[函数](./functions.md)
