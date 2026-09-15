---
title: 数据结构
description: 掌握 Pandas 的 Series 和 DataFrame 两种核心数据结构的创建和操作。
sidebar:
  label: 数据结构
  order: 20
---

Pandas 有两种核心数据结构：**Series**（一维）和 **DataFrame**（二维）。

## Series

Series 是一个**带标签的一维数组**，类似于 Python 的字典或 Excel 的一列。

### 创建 Series

```python
import pandas as pd

# 从列表创建
s = pd.Series([10, 20, 30])
print(s)
# 0    10
# 1    20
# 2    30

# 指定索引
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'])
print(s)
# a    10
# b    20
# c    30

# 从字典创建
data = {'张三': 85, '李四': 90, '王五': 78}
s = pd.Series(data)
print(s)
# 张三    85
# 李四    90
# 王五    78
```

### Series 操作

```python
# 访问元素
s['张三']        # 85
s[0]             # 85（位置索引）

# 切片
s['张三':'李四']  # 张三到李四（含两端）

# 运算
s + 10           # 每个元素加 10
s * 2            # 每个元素乘 2

# 常用方法
s.mean()         # 平均值
s.sum()          # 求和
s.max()          # 最大值
s.min()          # 最小值
s.std()          # 标准差
```

### Series 属性

```python
s.index          # 索引
s.values         # 值（NumPy 数组）
s.dtype          # 数据类型
s.shape          # 形状
s.size           # 元素个数
s.name           # Series 名称
```

## DataFrame

DataFrame 是一个**带标签的二维表格**，类似于 Excel 表或 SQL 表。

### 创建 DataFrame

```python
# 从字典创建（最常用）
data = {
    '姓名': ['张三', '李四', '王五'],
    '年龄': [25, 30, 28],
    '城市': ['北京', '上海', '广州']
}
df = pd.DataFrame(data)
print(df)
#    姓名  年龄  城市
# 0  张三   25  北京
# 1  李四   30  上海
# 2  王五   28  广州

# 从嵌套字典创建（外层key是列名，内层key是索引）
data = {
    '数学': {'张三': 85, '李四': 90, '王五': 78},
    '英语': {'张三': 75, '李四': 85, '王五': 92}
}
df = pd.DataFrame(data)
print(df)
#     数学  英语
# 张三   85   75
# 李四   90   85
# 王五   78   92

# 从列表的列表创建
data = [
    ['张三', 25, '北京'],
    ['李四', 30, '上海'],
    ['王五', 28, '广州']
]
df = pd.DataFrame(data, columns=['姓名', '年龄', '城市'])
```

### DataFrame 属性

```python
df.shape         # 形状 (行数, 列数)
df.dtypes        # 每列数据类型
df.index         # 行索引
df.columns       # 列名
df.size          # 元素总数
df.ndim          # 维度（2）
df.head()        # 前 5 行
df.tail()        # 后 5 行
df.info()        # 详细信息
df.describe()    # 统计摘要
```

### 访问列

```python
# 单列（返回 Series）
df['姓名']
df.姓名

# 多列（返回 DataFrame）
df[['姓名', '年龄']]
```

### 访问行

```python
# loc：按标签访问
df.loc[0]           # 第 0 行
df.loc[0:2]         # 第 0 到 2 行（含两端）

# iloc：按位置访问
df.iloc[0]          # 第 0 行
df.iloc[0:2]        # 第 0 到 2 行（不含末端）
df.iloc[0:2, 0:2]   # 前 2 行、前 2 列
```

### 条件筛选

```python
# 筛选年龄大于 25 的行
df[df['年龄'] > 25]

# 多条件筛选
df[(df['年龄'] > 25) & (df['城市'] == '北京')]

# 使用 query 方法
df.query('年龄 > 25 and 城市 == "北京"')
```

## 数据类型

| 类型 | 说明 | 示例 |
| --- | --- | --- |
| `int64` | 整数 | 1, 2, 3 |
| `float64` | 浮点数 | 1.5, 2.7 |
| `object` | 字符串 | 'hello', '你好' |
| `bool` | 布尔值 | True, False |
| `datetime64` | 日期时间 | 2024-01-01 |
| `category` | 分类类型 | '男', '女' |

### 类型转换

```python
# 查看类型
df.dtypes

# 转换类型
df['年龄'] = df['年龄'].astype(float)
df['日期'] = pd.to_datetime(df['日期'])
df['类别'] = df['类别'].astype('category')
```

## 索引操作

```python
# 设置索引
df = df.set_index('姓名')

# 重置索引
df = df.reset_index()

# 重命名索引
df = df.rename(index={'张三': 'Zhang San'})

# 重命名列
df = df.rename(columns={'姓名': 'name', '年龄': 'age'})
```

掌握了数据结构，下一章学习如何[数据读写](/pandas/io/)各种格式的文件。
