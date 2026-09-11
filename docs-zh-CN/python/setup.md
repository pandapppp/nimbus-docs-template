---
title: 安装与环境配置
description: 安装 Python 解释器，掌握 pip 包管理工具与 venv 虚拟环境的日常使用。
sidebar:
  label: 安装与环境配置
  order: 20
---

本章介绍 Python 解释器的安装、pip 包管理工具的使用，以及虚拟环境的创建与管理。

## 安装 Python 解释器

访问 Python 官网 [python.org/downloads](https://www.python.org/downloads/) 下载对应操作系统的安装包。安装时务必勾选 **"Add Python to PATH"** 选项，将 Python 加入系统环境变量。

安装完成后，打开终端验证：

```bash
# 查看版本
python --version
# 部分系统（macOS/Linux）使用 python3
python3 --version
# 输出示例：Python 3.12.0
```

## pip 包管理工具

`pip` 是 Python 的官方包管理器，用于安装和管理第三方库。

```bash
# 安装第三方库
pip install requests

# 安装指定版本
pip install "django==4.2.0"

# 升级库
pip install --upgrade pip

# 查看已安装的库
pip list

# 卸载库
pip uninstall requests

# 导出依赖列表（常用于项目交付）
pip freeze > requirements.txt

# 依据依赖列表批量安装
pip install -r requirements.txt
```

## 虚拟环境

虚拟环境用于隔离不同项目的依赖，避免包版本冲突。Python 3.3+ 内置了 `venv` 模块。

```bash
# 创建虚拟环境（名为 myenv）
python -m venv myenv

# 激活虚拟环境
# Windows:
myenv\Scripts\activate
# macOS / Linux:
# source myenv/bin/activate

# 退出虚拟环境
deactivate
```

激活后，终端提示符前会出现环境名称，此后的 `pip install` 只影响该环境。

> **推荐**：对于更复杂的项目管理，可以使用 `pipenv`（结合 pip 和 venv）或 `conda`（Anaconda 发行版自带的包与环境管理器，特别适合数据科学场景）。

## 常见问题

| 问题 | 处理方式 |
| --- | --- |
| `python` 不是内部或外部命令 | 安装时未勾选 Add to PATH，重新安装或手动添加环境变量 |
| pip 下载速度慢 | 配置国内镜像源，例如 `pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple` |
| 多版本 Python 共存 | 用 `py -3.12`（Windows）或 `python3.12`（macOS/Linux）指定版本 |

---

上一章：[Python 简介与特点](./introduction.md) · 下一章：[基本语法](./syntax.md)
