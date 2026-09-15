---
title: 数据可视化
description: 使用 Pandas 内置绑图功能快速创建常见的统计图表。
sidebar:
  label: 数据可视化
  order: 70
---

Pandas 内置了基于 Matplotlib 的绘图功能，可以快速创建常见的统计图表。

## 基础设置

```python
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体（Windows）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 设置中文字体（Mac）
plt.rcParams['font.sans-serif'] = ['Arial Unicode MS']
```

## 折线图

```python
df = pd.DataFrame({
    '月份': ['1月', '2月', '3月', '4月', '5月', '6月'],
    '销售额': [100, 120, 115, 130, 145, 160],
    '利润': [20, 25, 22, 28, 32, 38]
})

# 基本折线图
df.plot(x='月份', y='销售额', kind='line', title='月度销售趋势')

# 多条线
df.plot(x='月份', y=['销售额', '利润'], kind='line', figsize=(10, 6))

# 使用快捷方法
df.set_index('月份')['销售额'].plot.line(title='月度销售趋势')
```

## 柱状图

```python
# 垂直柱状图
df.plot(x='月份', y='销售额', kind='bar', title='月度销售额')

# 水平柱状图
df.plot(x='月份', y='销售额', kind='barh', title='月度销售额')

# 分组柱状图
df.plot(x='月份', y=['销售额', '利润'], kind='bar', figsize=(10, 6))

# 堆叠柱状图
df.plot(x='月份', y=['销售额', '利润'], kind='bar', stacked=True)
```

## 饼图

```python
df = pd.DataFrame({
    '部门': ['技术', '销售', '市场', '行政'],
    '人数': [50, 30, 20, 10]
})

# 基本饼图
df.set_index('部门')['人数'].plot.pie(title='部门人数分布')

# 带百分比
df.set_index('部门')['人数'].plot.pie(
    autopct='%1.1f%%',
    figsize=(8, 8),
    title='部门人数分布'
)
```

## 直方图

```python
df = pd.DataFrame({
    '工资': [5000, 6000, 7000, 8000, 9000, 10000, 12000, 15000, 20000]
})

# 基本直方图
df['工资'].plot.hist(title='工资分布')

# 指定分箱数
df['工资'].plot.hist(bins=5, title='工资分布')

# 多列直方图
df[['工资', '奖金']].plot.hist(alpha=0.5, figsize=(10, 6))
```

## 散点图

```python
df = pd.DataFrame({
    '年龄': [25, 30, 35, 40, 45, 50],
    '工资': [8000, 10000, 12000, 15000, 18000, 20000],
    '工龄': [2, 5, 8, 12, 15, 20]
})

# 基本散点图
df.plot.scatter(x='年龄', y='工资', title='年龄与工资关系')

# 带颜色和大小
df.plot.scatter(x='年龄', y='工资', c='工龄', s=df['工龄']*20, colormap='viridis')
```

## 箱线图

```python
df = pd.DataFrame({
    '部门': ['技术']*10 + ['销售']*10,
    '工资': [8000, 9000, 10000, 11000, 12000, 8500, 9500, 10500, 11500, 12500,
             6000, 7000, 8000, 9000, 10000, 6500, 7500, 8500, 9500, 10500]
})

# 按部门绘制箱线图
df.boxplot(column='工资', by='部门', figsize=(8, 6))
```

## 面积图

```python
df = pd.DataFrame({
    '月份': ['1月', '2月', '3月', '4月', '5月'],
    '产品A': [10, 15, 12, 18, 22],
    '产品B': [8, 10, 14, 12, 16],
    '产品C': [5, 8, 6, 10, 12]
})

# 堆叠面积图
df.set_index('月份').plot.area(title='产品销售趋势')
```

## 高级定制

### 子图

```python
fig, axes = plt.subplots(2, 2, figsize=(12, 10))

df.plot(x='月份', y='销售额', ax=axes[0, 0], title='销售趋势')
df.plot(x='月份', y='利润', ax=axes[0, 1], title='利润趋势')
df.plot(x='月份', y='销售额', kind='bar', ax=axes[1, 0], title='月度销售')
df.set_index('月份')['销售额'].plot.pie(ax=axes[1, 1], autopct='%1.1f%%')

plt.tight_layout()
plt.show()
```

### 样式定制

```python
# 设置图表大小
df.plot(figsize=(12, 6))

# 设置标题和标签
df.plot(title='图表标题', xlabel='X轴', ylabel='Y轴')

# 设置颜色
df.plot(color=['red', 'blue', 'green'])

# 设置网格
df.plot(grid=True)

# 设置图例位置
df.plot(legend='upper right')

# 保存图表
fig = df.plot().get_figure()
fig.savefig('chart.png', dpi=150, bbox_inches='tight')
```

## 常用图表类型

| 方法 | 图表类型 | 适用场景 |
| --- | --- | --- |
| `plot.line()` | 折线图 | 趋势分析 |
| `plot.bar()` | 柱状图 | 分类对比 |
| `plot.barh()` | 水平柱状图 | 分类对比（标签长） |
| `plot.pie()` | 饼图 | 占比分析 |
| `plot.hist()` | 直方图 | 分布分析 |
| `plot.scatter()` | 散点图 | 相关性分析 |
| `plot.box()` | 箱线图 | 离群值检测 |
| `plot.area()` | 面积图 | 累计趋势 |

## 快捷方法

```python
# 使用快捷方法
df['工资'].plot()           # 默认折线图
df['工资'].plot.hist()     # 直方图
df['工资'].plot.box()      # 箱线图
df['工资'].plot.kde()      # 核密度估计

# DataFrame 快捷方法
df.plot()                  # 所有列的折线图
df.plot.bar()              # 柱状图
df.plot.scatter(x='x', y='y')  # 散点图
```

恭喜！你已经掌握了 Pandas 的核心功能。回到[Pandas 首页](/pandas/)查看完整教程目录。
