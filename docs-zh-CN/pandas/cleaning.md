---
title: 数据清洗
description: 学习处理缺失值、重复值、异常值和数据类型转换等数据清洗技术。
sidebar:
  label: 数据清洗
  order: 50
---

真实数据往往是"脏"的，需要清洗后才能使用。Pandas 提供了强大的数据清洗工具。

## 缺失值处理

### 检测缺失值

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    '姓名': ['张三', '李四', None, '赵六'],
    '年龄': [25, np.nan, 28, 35],
    '城市': ['北京', '上海', '广州', None]
})

# 检测缺失值
df.isna()          # 布尔矩阵
df.isna().sum()    # 每列缺失数量
df.isna().sum().sum()  # 总缺失数量

# 非空检测
df.notna()
```

### 删除缺失值

```python
# 删除含缺失值的行
df.dropna()

# 删除全为缺失值的行
df.dropna(how='all')

# 删除指定列有缺失值的行
df.dropna(subset=['姓名', '年龄'])

# 删除含缺失值的列
df.dropna(axis=1)

# 至少有 2 个非空值的行
df.dropna(thresh=2)
```

### 填充缺失值

```python
# 用固定值填充
df.fillna(0)
df.fillna('未知')

# 用每列的均值填充
df.fillna(df.mean(numeric_only=True))

# 用前一个值填充（前向填充）
df.fillna(method='ffill')

# 用后一个值填充（后向填充）
df.fillna(method='bfill')

# 用中位数填充
df.fillna(df.median(numeric_only=True))

# 不同列用不同值填充
df.fillna({'姓名': '未知', '年龄': 0, '城市': '未知'})
```

### 插值

```python
# 线性插值
df['年龄'].interpolate(method='linear')

# 时间插值
df['日期'].interpolate(method='time')
```

## 重复值处理

### 检测重复值

```python
# 检测重复行
df.duplicated()

# 检测指定列的重复值
df.duplicated(subset=['姓名'])

# 显示所有重复行（包括第一次出现的）
df[df.duplicated(keep=False)]
```

### 删除重复值

```python
# 删除重复行（保留第一次出现的）
df.drop_duplicates()

# 保留最后一次出现的
df.drop_duplicates(keep='last')

# 删除指定列的重复值
df.drop_duplicates(subset=['姓名'])

# 原地修改
df.drop_duplicates(inplace=True)
```

## 数据类型转换

```python
# 查看类型
df.dtypes

# 转换为数值
df['年龄'] = pd.to_numeric(df['年龄'], errors='coerce')

# 转换为日期
df['日期'] = pd.to_datetime(df['日期'])

# 转换为字符串
df['编号'] = df['编号'].astype(str)

# 转换为分类类型
df['性别'] = df['性别'].astype('category')

# 转换为布尔
df['是否会员'] = df['是否会员'].astype(bool)
```

## 字符串处理

### 常用方法

```python
# 去除空格
df['姓名'] = df['姓名'].str.strip()
df['姓名'] = df['姓名'].str.lstrip()  # 左侧
df['姓名'] = df['姓名'].str.rstrip()  # 右侧

# 大小写转换
df['英文名'] = df['英文名'].str.lower()
df['英文名'] = df['英文名'].str.upper()
df['英文名'] = df['英文名'].str.title()  # 首字母大写

# 替换
df['电话'] = df['电话'].str.replace('-', '')

# 分割
df['姓名'].str.split(' ', expand=True)  # 按空格分割

# 提取
df['邮箱'].str.extract(r'@(.+)$')  # 提取域名

# 包含
df['描述'].str.contains('关键词')

# 长度
df['姓名'].str.len()
```

## 异常值处理

### 检测异常值

```python
# 统计方法
Q1 = df['工资'].quantile(0.25)
Q3 = df['工资'].quantile(0.75)
IQR = Q3 - Q1

# 定义异常值范围
lower = Q1 - 1.5 * IQR
upper = Q3 + 1.5 * IQR

# 筛选异常值
outliers = df[(df['工资'] < lower) | (df['工资'] > upper)]

# Z-score 方法
from scipy import stats
z_scores = stats.zscore(df['工资'])
outliers = df[abs(z_scores) > 3]
```

### 处理异常值

```python
# 删除异常值
df = df[(df['工资'] >= lower) & (df['工资'] <= upper)]

# 截断（Winsorize）
df['工资'] = df['工资'].clip(lower, upper)

# 替换为中位数
median = df['工资'].median()
df.loc[df['工资'] > upper, '工资'] = median
```

## 列操作

```python
# 新增列
df['年薪'] = df['工资'] * 12

# 基于条件新增列
df['级别'] = np.where(df['年龄'] > 30, '高级', '初级')

# 删除列
df = df.drop(columns=['旧列名'])

# 重命名列
df = df.rename(columns={'旧名': '新名'})

# 重新排列列
df = df[['姓名', '年龄', '工资', '城市']]
```

## 行操作

```python
# 新增行
new_row = {'姓名': '钱七', '年龄': 22, '城市': '杭州'}
df = pd.concat([df, pd.DataFrame([new_row])], ignore_index=True)

# 删除行
df = df.drop(index=[0, 1])  # 删除第 0、1 行

# 条件删除
df = df[df['年龄'] >= 18]

# 排序
df = df.sort_values('年龄')            # 升序
df = df.sort_values('年龄', ascending=False)  # 降序
df = df.sort_values(['城市', '年龄'])   # 多列排序
```

## 实战：清洗流程

```python
# 完整清洗流程
def clean_data(df):
    # 1. 删除完全重复的行
    df = df.drop_duplicates()
    
    # 2. 删除关键列有缺失值的行
    df = df.dropna(subset=['姓名', '年龄'])
    
    # 3. 填充其他缺失值
    df = df.fillna({'城市': '未知', '工资': 0})
    
    # 4. 转换数据类型
    df['年龄'] = pd.to_numeric(df['年龄'], errors='coerce')
    df['日期'] = pd.to_datetime(df['日期'])
    
    # 5. 去除空格
    df['姓名'] = df['姓名'].str.strip()
    
    # 6. 删除异常值
    df = df[df['年龄'].between(18, 100)]
    
    # 7. 重置索引
    df = df.reset_index(drop=True)
    
    return df
```

数据清洗完成，下一章学习[数据处理](/pandas/processing/)，进行分组、聚合和合并操作。
