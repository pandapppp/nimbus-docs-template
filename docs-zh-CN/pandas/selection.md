---
title: 数据选择
description: 掌握 Pandas 的索引、切片和条件筛选方法，高效访问数据。
sidebar:
  label: 数据选择
  order: 40
---

数据选择是 Pandas 最常用的操作之一。掌握 `loc`、`iloc` 和条件筛选是高效使用 Pandas 的关键。

## 列选择

```python
import pandas as pd

df = pd.DataFrame({
    '姓名': ['张三', '李四', '王五', '赵六'],
    '年龄': [25, 30, 28, 35],
    '城市': ['北京', '上海', '广州', '深圳'],
    '工资': [8000, 12000, 9500, 15000]
})

# 单列（返回 Series）
df['姓名']
df.姓名

# 多列（返回 DataFrame）
df[['姓名', '年龄']]
```

## 行选择

### loc：按标签

```python
# 单行
df.loc[0]

# 多行
df.loc[0:2]          # 第 0 到 2 行（含两端）
df.loc[[0, 2]]       # 第 0 和 2 行

# 指定行列
df.loc[0, '姓名']       # 单个值
df.loc[0:2, ['姓名', '年龄']]  # 多行多列
```

### iloc：按位置

```python
# 单行
df.iloc[0]

# 多行
df.iloc[0:2]         # 第 0 到 1 行（不含末端）
df.iloc[[0, 2]]      # 第 0 和 2 行

# 指定行列
df.iloc[0, 0]            # 单个值
df.iloc[0:2, 0:2]        # 前 2 行、前 2 列
df.iloc[0:2, [0, 2]]     # 前 2 行、第 0 和 2 列
```

### loc vs iloc

| 方法 | 索引方式 | 切片 | 示例 |
| --- | --- | --- | --- |
| `loc` | 标签 | 含两端 | `df.loc[0:2]` → 0, 1, 2 |
| `iloc` | 位置 | 不含末端 | `df.iloc[0:2]` → 0, 1 |

## 条件筛选

### 单条件

```python
# 年龄大于 25
df[df['年龄'] > 25]

# 城市是北京
df[df['城市'] == '北京']

# 非空值
df[df['姓名'].notna()]
```

### 多条件

```python
# AND（同时满足）
df[(df['年龄'] > 25) & (df['城市'] == '北京')]

# OR（满足其一）
df[(df['年龄'] > 30) | (df['工资'] > 10000)]

# NOT（取反）
df[~(df['城市'] == '北京')]
```

### isin 方法

```python
# 筛选在列表中的值
df[df['城市'].isin(['北京', '上海'])]

# 不在列表中
df[~df['城市'].isin(['北京', '上海'])]
```

### 字符串筛选

```python
# 包含某字符串
df[df['姓名'].str.contains('张')]

# 以某字符串开头
df[df['姓名'].str.startswith('张')]

# 正则匹配
df[df['姓名'].str.contains(r'^张|李$', regex=True)]
```

## query 方法

`query` 方法用字符串表达式筛选，更简洁：

```python
# 基本用法
df.query('年龄 > 25')
df.query('城市 == "北京"')

# 多条件
df.query('年龄 > 25 and 城市 == "北京"')
df.query('年龄 > 30 or 工资 > 10000')

# 使用变量
min_age = 25
df.query('年龄 > @min_age')

# 列名有空格时用反引号
df.rename(columns={'年龄': '年 龄'}).query('`年 龄` > 25')
```

## 赋值

### loc/iloc 赋值

```python
# 修改单个值
df.loc[0, '年龄'] = 26

# 修改整列
df['工资'] = df['工资'] * 1.1  # 涨薪 10%

# 条件赋值
df.loc[df['年龄'] > 30, '级别'] = '高级'
```

## where / mask

```python
# where：条件为 True 保留原值，否则替换
df['年龄'].where(df['年龄'] > 25, 0)

# mask：条件为 True 替换，否则保留原值
df['年龄'].mask(df['年龄'] > 25, 0)
```

## 随机抽样

```python
# 随机抽 2 行
df.sample(n=2)

# 按比例抽样
df.sample(frac=0.5)

# 可重复抽样
df.sample(n=5, replace=True)
```

## 实用技巧

| 技巧 | 代码 |
| --- | --- |
| 选择数值列 | `df.select_dtypes(include='number')` |
| 选择字符串列 | `df.select_dtypes(include='object')` |
| 排除某些列 | `df.drop(columns=['列名'])` |
| 去重 | `df['列名'].unique()` |
| 计数 | `df['列名'].value_counts()` |

数据选择掌握后，下一章学习[数据清洗](/pandas/cleaning/)，处理脏数据。
