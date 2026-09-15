---
title: 数据读写
description: 学习使用 Pandas 读写 CSV、Excel、JSON、SQL 等常见数据格式。
sidebar:
  label: 数据读写
  order: 30
---

Pandas 支持读写多种数据格式，最常用的是 **CSV** 和 **Excel**。

## CSV 文件

### 读取 CSV

```python
import pandas as pd

# 基本读取
df = pd.read_csv('data.csv')

# 指定编码（中文文件常用）
df = pd.read_csv('data.csv', encoding='utf-8')
df = pd.read_csv('data.csv', encoding='gbk')

# 指定分隔符
df = pd.read_csv('data.csv', sep='\t')  # Tab 分隔

# 指定列名
df = pd.read_csv('data.csv', names=['A', 'B', 'C'])

# 跳过行
df = pd.read_csv('data.csv', skiprows=1)  # 跳过第 1 行
df = pd.read_csv('data.csv', header=0)    # 第 0 行作为列名

# 只读部分列
df = pd.read_csv('data.csv', usecols=['姓名', '年龄'])

# 指定数据类型
df = pd.read_csv('data.csv', dtype={'年龄': float})

# 读取大文件（分块读取）
for chunk in pd.read_csv('data.csv', chunksize=1000):
    process(chunk)  # 每次处理 1000 行
```

### 写入 CSV

```python
# 基本写入
df.to_csv('output.csv')

# 不保存索引
df.to_csv('output.csv', index=False)

# 指定编码
df.to_csv('output.csv', encoding='utf-8-sig')  # Excel 友好

# 指定分隔符
df.to_csv('output.csv', sep='\t')

# 保存部分列
df.to_csv('output.csv', columns=['姓名', '年龄'])

# 追加模式
df.to_csv('output.csv', mode='a', header=False)
```

## Excel 文件

### 读取 Excel

```python
# 基本读取
df = pd.read_excel('data.xlsx')

# 指定工作表
df = pd.read_excel('data.xlsx', sheet_name='Sheet1')
df = pd.read_excel('data.xlsx', sheet_name=0)  # 第一个工作表

# 读取多个工作表
sheets = pd.read_excel('data.xlsx', sheet_name=None)  # 返回字典
for name, df in sheets.items():
    print(f'{name}: {df.shape}')

# 指定列名行
df = pd.read_excel('data.xlsx', header=1)  # 第 1 行作为列名

# 跳过行
df = pd.read_excel('data.xlsx', skiprows=2)  # 跳过前 2 行
```

### 写入 Excel

```python
# 基本写入
df.to_excel('output.xlsx', index=False)

# 指定工作表名
df.to_excel('output.xlsx', sheet_name='数据')

# 写入多个工作表
with pd.ExcelWriter('output.xlsx') as writer:
    df1.to_excel(writer, sheet_name='表1', index=False)
    df2.to_excel(writer, sheet_name='表2', index=False)

# 追加到已有文件
with pd.ExcelWriter('output.xlsx', mode='a', engine='openpyxl') as writer:
    df.to_excel(writer, sheet_name='新表', index=False)
```

> ⚠️ 需要安装 `openpyxl`：`pip install openpyxl`

## JSON 文件

### 读取 JSON

```python
# 基本读取
df = pd.read_json('data.json')

# 从嵌套 JSON 读取
import json
with open('data.json', 'r') as f:
    data = json.load(f)
df = pd.json_normalize(data, record_path='items')

# 从 API 响应读取
import requests
resp = requests.get('https://api.example.com/data')
df = pd.DataFrame(resp.json())
```

### 写入 JSON

```python
# 基本写入
df.to_json('output.json')

# 指定方向（记录格式）
df.to_json('output.json', orient='records', force_ascii=False)

# 缩进格式
df.to_json('output.json', orient='records', indent=2, force_ascii=False)
```

## SQL 数据库

### 读取 SQL

```python
import sqlite3

# 连接数据库
conn = sqlite3.connect('database.db')

# 读取表
df = pd.read_sql('SELECT * FROM users', conn)

# 读取表名
df = pd.read_sql_table('users', conn)

# 使用参数
df = pd.read_sql('SELECT * FROM users WHERE age > ?', conn, params=[25])
```

### 写入 SQL

```python
# 写入表
df.to_sql('users', conn, if_exists='replace', index=False)

# if_exists 参数：
# 'replace' - 替换表
# 'append'  - 追加数据
# 'fail'    - 表存在则报错
```

## 其他格式

### 剪贴板

```python
# 从剪贴板读取（复制表格后直接读取）
df = pd.read_clipboard()

# 写入剪贴板
df.to_clipboard()
```

### HTML 表格

```python
# 读取网页中的表格
tables = pd.read_html('https://example.com/table')
df = tables[0]  # 第一个表格
```

### Parquet（大数据常用）

```python
# 读取
df = pd.read_parquet('data.parquet')

# 写入
df.to_parquet('output.parquet')
```

## 常用参数总结

| 参数 | 说明 | 适用函数 |
| --- | --- | --- |
| `encoding` | 编码格式 | 所有 |
| `sep` / `delimiter` | 分隔符 | read_csv |
| `header` | 列名所在行 | read_csv, read_excel |
| `names` | 自定义列名 | read_csv |
| `usecols` | 读取指定列 | 所有 |
| `dtype` | 指定数据类型 | 所有 |
| `skiprows` | 跳过行 | 所有 |
| `nrows` | 读取行数 | read_csv |
| `index` | 是否保存索引 | to_csv, to_excel |
| `encoding` | 编码格式 | to_csv |

## 编码问题

中文数据常遇到编码问题：

```python
# 常见编码
'utf-8'        # 默认，推荐
'gbk'          # 中文 Windows
'gb2312'       # 简体中文
'utf-8-sig'    # 带 BOM 的 UTF-8（Excel 友好）
'latin-1'      # 西欧字符

# 自动检测编码
import chardet
with open('data.csv', 'rb') as f:
    result = chardet.detect(f.read())
    print(result['encoding'])
```

数据读写掌握后，下一章学习[数据选择](/pandas/selection/)，如何高效地筛选和访问数据。
