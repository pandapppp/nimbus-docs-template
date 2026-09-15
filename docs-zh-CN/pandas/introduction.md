---
title: Pandas 简介
description: 了解 Pandas 库的背景、核心功能和应用场景。
sidebar:
  label: Pandas 简介
  order: 10
---

**Pandas** 是 Python 中最强大的**数据分析和处理库**，提供了高性能、易用的数据结构，是数据科学、机器学习、数据清洗的必备工具。

## 为什么学 Pandas？

| 场景 | Pandas 能做什么 |
| --- | --- |
| 数据清洗 | 处理缺失值、重复值、异常值 |
| 数据分析 | 统计、分组、聚合、透视表 |
| 数据转换 | 筛选、排序、合并、重塑 |
| 数据读写 | CSV、Excel、SQL、JSON 等格式 |
| 数据可视化 | 快速绘制图表 |

## 安装

```bash
pip install pandas
```

验证安装：

```python
import pandas as pd
print(pd.__version__)
```

## 核心数据结构

Pandas 有两个核心数据结构：

| 数据结构 | 说明 | 类比 |
| --- | --- | --- |
| **Series** | 一维带标签数组 | Excel 的一列 |
| **DataFrame** | 二维带标签表格 | Excel 的一张表 |

### Series 示例

```python
import pandas as pd

# 创建 Series
s = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'])
print(s)
# a    10
# b    20
# c    30
# d    40
```

### DataFrame 示例

```python
# 创建 DataFrame
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
```

## Pandas 能做什么？

### 快速示例

```python
# 读取 CSV 文件
df = pd.read_csv('data.csv')

# 查看前 5 行
df.head()

# 基本统计
df.describe()

# 筛选数据
df[df['年龄'] > 25]

# 分组统计
df.groupby('城市')['年龄'].mean()

# 处理缺失值
df.dropna()  # 删除含缺失值的行
df.fillna(0)  # 用 0 填充缺失值

# 保存数据
df.to_csv('output.csv', index=False)
```

## 与其他工具对比

| 工具 | 优势 | 劣势 |
| --- | --- | --- |
| **Pandas** | 功能全面、生态好 | 大数据内存不足 |
| **Excel** | 可视化、交互式 | 处理大数据慢 |
| **SQL** | 数据库查询 | 不灵活 |
| **NumPy** | 数值计算快 | 缺乏数据操作功能 |
| **Polars** | 比 Pandas 快 | 生态不如 Pandas |

## 应用场景

| 场景 | 示例 |
| --- | --- |
| **数据分析** | 销售报表、用户行为分析 |
| **数据清洗** | 处理脏数据、格式转换 |
| **机器学习** | 特征工程、数据预处理 |
| **自动化** | 批量处理 Excel、生成报表 |
| **爬虫数据** | 存储和处理爬取的数据 |

## 学习路线

1. **基础**：数据结构、创建、读写
2. **选择**：索引、切片、条件筛选
3. **清洗**：缺失值、重复值、类型转换
4. **处理**：分组、合并、透视表
5. **应用**：实际案例、性能优化

准备好了吗？下一章我们来学习[数据结构](/pandas/data-structures/)。
