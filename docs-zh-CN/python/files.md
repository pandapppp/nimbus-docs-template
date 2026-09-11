---
title: 文件操作
description: 复习 with open 读写文件、打开模式、os 模块路径操作与 pathlib 的现代写法。
sidebar:
  label: 文件操作
  order: 90
---

## 文件读写

使用内置 `open()` 函数打开文件。推荐使用 `with` 语句（上下文管理器），代码块结束时自动关闭文件。

### 打开模式

| 模式 | 说明 |
| --- | --- |
| `"r"` | 只读（默认） |
| `"w"` | 只写（覆盖原内容，文件不存在则创建） |
| `"a"` | 追加（文件不存在则创建） |
| `"r+"` | 读写 |
| `"rb"` / `"wb"` | 二进制读 / 写 |

### 读写示例

```python
# 写入文件（文本模式务必指定 encoding）
with open("example.txt", "w", encoding="utf-8") as f:
    f.write("第一行\n")
    f.write("第二行\n")

# 读取全部内容
with open("example.txt", "r", encoding="utf-8") as f:
    content = f.read()
    print(content)

# 逐行读取（内存友好，推荐用于大文件）
with open("example.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())

# 读取所有行到列表
with open("example.txt", "r", encoding="utf-8") as f:
    lines = f.readlines()  # ['第一行\n', '第二行\n']

# 追加内容
with open("example.txt", "a", encoding="utf-8") as f:
    f.write("第三行\n")

# 读写二进制文件（如图片）
with open("image.png", "rb") as f:
    data = f.read()
```

> **提醒**：在 Windows 上处理中文文本时，不指定 `encoding="utf-8"` 可能得到乱码（默认跟随系统代码页）。

## os 模块 — 路径与目录操作

```python
import os

# 路径操作
os.path.join("folder", "file.txt")   # 拼接路径（跨平台）
os.path.exists("example.txt")        # 是否存在
os.path.isdir("folder")              # 是否目录
os.path.isfile("example.txt")        # 是否文件
os.path.basename("/a/b/c.txt")       # 'c.txt'
os.path.dirname("/a/b/c.txt")        # '/a/b'
os.path.getsize("example.txt")       # 文件大小（字节）

# 目录操作
os.getcwd()                # 当前工作目录
os.listdir(".")            # 列出目录内容
os.mkdir("new_dir")        # 创建目录
os.makedirs("a/b/c")       # 递归创建多级目录
os.remove("file.txt")      # 删除文件
os.rmdir("empty_dir")      # 删除空目录
```

## pathlib — 现代写法（推荐）

Python 3.4+ 引入的 `pathlib` 提供面向对象的路径操作，比 `os.path` 更直观：

```python
from pathlib import Path

p = Path("folder") / "file.txt"   # 用 / 拼接路径

print(p.exists())                 # 是否存在
print(p.name)                     # file.txt
print(p.suffix)                   # .txt
print(p.parent)                   # folder
print(p.resolve())                # 绝对路径

p.read_text(encoding="utf-8")     # 一步读取文本
p.write_text("内容", encoding="utf-8")  # 一步写入文本

# 遍历目录
for f in Path(".").glob("*.txt"):
    print(f)
```

## 临时文件

```python
import tempfile

# 自动清理的临时文件
with tempfile.NamedTemporaryFile(mode="w", suffix=".txt", delete=False) as tmp:
    tmp.write("临时内容")
    print(tmp.name)
```

---

上一章：[模块与包](./modules.md) · 下一章：[异常处理](./exceptions.md)
