---
title: 面向对象编程基础
description: 复习类与对象、继承、多态、类方法与静态方法，以及常用魔术方法。
sidebar:
  label: 面向对象编程基础
  order: 110
---

面向对象编程（OOP）是以**对象**为核心的编程范式。Python 是一门面向对象的语言，一切皆对象。

## 类与对象

```python
class Dog:
    """狗类"""

    # 类变量（所有实例共享）
    species = "Canis familiaris"

    # 初始化方法（构造函数）
    def __init__(self, name, age):
        self.name = name    # 实例变量
        self.age = age

    # 实例方法
    def bark(self):
        return f"{self.name} says: Woof!"

    # 字符串表示
    def __str__(self):
        return f"{self.name}, {self.age}岁"

# 创建对象（实例化）
dog1 = Dog("Buddy", 3)
dog2 = Dog("Max", 5)

print(dog1.bark())     # Buddy says: Woof!
print(dog2)            # Max, 5岁（调用 __str__）
print(dog1.species)    # Canis familiaris（类变量）
```

要点：

- `__init__` 在实例化时自动调用，`self` 指向实例本身
- **类变量**定义在类层级、所有实例共享；**实例变量**通过 `self.x` 绑定到单个实例
- 可变类型（如 list）不要做类变量，除非确实需要共享

## 继承

```python
# 父类（基类）
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "..."

# 子类（派生类）
class Cat(Animal):
    def __init__(self, name, color):
        super().__init__(name)   # 调用父类构造方法
        self.color = color

    def speak(self):             # 方法重写（override）
        return f"{self.name} says: Meow!"

cat = Cat("Whiskers", "white")
print(cat.speak())   # Whiskers says: Meow!
print(cat.color)     # white
print(isinstance(cat, Animal))  # True — 子类实例也是父类类型
print(issubclass(Cat, Animal))  # True
```

## 多态

不同类的对象调用同名方法，表现出不同的行为：

```python
class Dog(Animal):
    def speak(self):
        return f"{self.name}: Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name}: Meow!"

def animal_speak(animal):
    print(animal.speak())   # 多态：传入不同对象，行为不同

animals = [Dog("Buddy"), Cat("Whiskers"), Dog("Max")]
for a in animals:
    animal_speak(a)
# Buddy: Woof!
# Whiskers: Meow!
# Max: Woof!
```

## 类方法与静态方法

```python
class MathHelper:
    pi = 3.14159

    # 实例方法：第一个参数是 self
    def circle_area(self, r):
        return self.pi * r ** 2

    # 类方法：第一个参数是 cls，可访问/修改类状态
    @classmethod
    def describe(cls):
        return f"pi = {cls.pi}"

    # 静态方法：不接收 self/cls，就是放在类命名空间里的普通函数
    @staticmethod
    def is_positive(n):
        return n > 0

print(MathHelper.describe())       # pi = 3.14159
print(MathHelper.is_positive(5))   # True

helper = MathHelper()
print(helper.circle_area(2))       # 12.56636
```

## 常用魔术方法

| 方法 | 触发场景 |
| --- | --- |
| `__init__(self, ...)` | 构造/初始化 |
| `__str__(self)` | `str()` / `print()` 调用，返回用户友好字符串 |
| `__repr__(self)` | `repr()` 调用，返回开发者友好字符串 |
| `__len__(self)` | `len()` 调用 |
| `__eq__(self, other)` | `==` 运算 |
| `__lt__(self, other)` | `<` 运算（配合 `functools.total_ordering` 可补全其余比较） |
| `__getitem__(self, key)` | 索引访问 `obj[key]` |
| `__iter__(self)` | 迭代器支持，使对象可用于 for 循环 |
| `__call__(self, ...)` | 对象可像函数一样调用 |
| `__enter__` / `__exit__` | 上下文管理器，支持 `with` 语句 |

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius

    def __eq__(self, other):
        return self.celsius == other.celsius

    def __repr__(self):
        return f"Temperature({self.celsius}°C)"

t1, t2 = Temperature(25), Temperature(25)
print(t1 == t2)   # True
print(t1)         # Temperature(25°C)
```

## 数据类（dataclass）

日常用来存数据的类，用 `dataclass` 装饰器可以省去样板代码（Python 3.7+）：

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float

p = Point(1.0, 2.0)
print(p)        # Point(x=1.0, y=2.0) — 自动生成 __repr__
print(p == Point(1.0, 2.0))  # True — 自动生成 __eq__
```

---

上一章：[异常处理](./exceptions.md) · 下一章：[常用内置函数](./builtins.md)
