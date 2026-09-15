---
title: 数据处理
description: 掌握 Pandas 的分组聚合、合并连接、透视表等高级数据处理操作。
sidebar:
  label: 数据处理
  order: 60
---

数据处理是 Pandas 最强大的功能，包括分组聚合、合并连接、透视表等。

## 分组聚合（GroupBy）

### 基本分组

```python
import pandas as pd

df = pd.DataFrame({
    '城市': ['北京', '上海', '北京', '上海', '广州'],
    '部门': ['技术', '销售', '销售', '技术', '技术'],
    '工资': [10000, 8000, 9000, 12000, 9500]
})

# 按城市分组，计算平均工资
df.groupby('城市')['工资'].mean()

# 按多列分组
df.groupby(['城市', '部门'])['工资'].mean()
```

### 常用聚合函数

```python
# 单个聚合
df.groupby('城市')['工资'].sum()
df.groupby('城市')['工资'].mean()
df.groupby('城市')['工资'].median()
df.groupby('城市')['工资'].min()
df.groupby('城市')['工资'].max()
df.groupby('城市')['工资'].count()
df.groupby('城市')['工资'].std()

# 多个聚合
df.groupby('城市')['工资'].agg(['mean', 'sum', 'count'])

# 不同列不同聚合
df.groupby('城市').agg({
    '工资': 'mean',
    '部门': 'count'
})

# 自定义聚合
df.groupby('城市')['工资'].agg(lambda x: x.max() - x.min())
```

### transform

```python
# 每行减去该组均值（标准化）
df['工资_标准化'] = df.groupby('城市')['工资'].transform(lambda x: x - x.mean())

# 组内排名
df['组内排名'] = df.groupby('城市')['工资'].rank(ascending=False)
```

## 合并（Merge）

### 类似 SQL JOIN

```python
df1 = pd.DataFrame({
    'ID': [1, 2, 3],
    '姓名': ['张三', '李四', '王五']
})

df2 = pd.DataFrame({
    'ID': [1, 2, 4],
    '工资': [10000, 12000, 9000]
})

# 内连接（默认）
pd.merge(df1, df2, on='ID')
#    ID  姓名    工资
# 0   1  张三  10000
# 1   2  李四  12000

# 左连接
pd.merge(df1, df2, on='ID', how='left')

# 右连接
pd.merge(df1, df2, on='ID', how='right')

# 外连接
pd.merge(df1, df2, on='ID', how='outer')

# 不同列名合并
pd.merge(df1, df2, left_on='ID', right_on='编号')
```

### 连接类型对比

| 类型 | 说明 | 类比 SQL |
| --- | --- | --- |
| `inner` | 两表都有的行 | INNER JOIN |
| `left` | 左表所有行 | LEFT JOIN |
| `right` | 右表所有行 | RIGHT JOIN |
| `outer` | 两表所有行 | FULL OUTER JOIN |

## 拼接（Concat）

```python
# 纵向拼接（行方向）
pd.concat([df1, df2])

# 横向拼接（列方向）
pd.concat([df1, df2], axis=1)

# 忽略索引
pd.concat([df1, df2], ignore_index=True)

# 标记来源
pd.concat([df1, df2], keys=['表1', '表2'])
```

## 透视表（Pivot Table）

```python
df = pd.DataFrame({
    '城市': ['北京', '上海', '北京', '上海'],
    '部门': ['技术', '技术', '销售', '销售'],
    '工资': [10000, 12000, 8000, 9000]
})

# 创建透视表
pd.pivot_table(df, values='工资', index='城市', columns='部门', aggfunc='mean')

# 多个聚合函数
pd.pivot_table(df, values='工资', index='城市', columns='部门', aggfunc=['mean', 'sum'])

# 添加合计
pd.pivot_table(df, values='工资', index='城市', columns='部门', aggfunc='sum', margins=True)
```

## 交叉表（Crosstab）

```python
# 统计频次
pd.crosstab(df['城市'], df['部门'])

# 添加合计
pd.crosstab(df['城市'], df['部门'], margins=True)

# 按比例
pd.crosstab(df['城市'], df['部门'], normalize='index')
```

## Apply 函数

```python
# 对每列应用函数
df.apply(lambda x: x.max() - x.min())

# 对每行应用函数
df.apply(lambda row: row['工资'] * 12, axis=1)

# 对每个元素应用函数
df['工资'].apply(lambda x: f'{x:,.0f}元')

# 使用自定义函数
def salary_level(salary):
    if salary >= 10000:
        return '高'
    elif salary >= 8000:
        return '中'
    else:
        return '低'

df['工资级别'] = df['工资'].apply(salary_level)
```

## Map 和 Replace

```python
# map：映射替换
df['城市代码'] = df['城市'].map({'北京': 'BJ', '上海': 'SH', '广州': 'GZ'})

# replace：替换值
df['部门'] = df['部门'].replace({'技术': '研发', '销售': '市场'})

# 批量替换
df.replace({'工资': {0: None}, '城市': {'': '未知'}})
```

## 窗口函数

```python
# 滚动平均
df['工资_移动平均'] = df['工资'].rolling(window=3).mean()

# 累计求和
df['工资_累计'] = df['工资'].cumsum()

# 累计最大值
df['工资_累计最大'] = df['工资'].cummax()

# 排名
df['排名'] = df['工资'].rank(ascending=False)
```

## 实战案例

### 销售数据分析

```python
# 读取数据
sales = pd.read_csv('sales.csv')

# 月度销售汇总
monthly = sales.groupby(sales['日期'].dt.to_period('M')).agg({
    '金额': 'sum',
    '订单数': 'count',
    '客户数': 'nunique'
})

# 产品销售排名
product_rank = sales.groupby('产品')['金额'].sum().sort_values(ascending=False)

# 客户消费分析
customer_stats = sales.groupby('客户ID').agg({
    '金额': ['sum', 'mean', 'count'],
    '日期': ['min', 'max']
})

# 透视表：城市 x 产品的销售额
pivot = pd.pivot_table(sales, values='金额', index='城市', columns='产品', aggfunc='sum')
```

数据处理掌握后，下一章学习[数据可视化](/pandas/visualization/)，让数据说话。
