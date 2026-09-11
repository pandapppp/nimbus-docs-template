# Nimbus Docs Template

<img src="./assets/nimbus-mark.svg" alt="Nimbus 官方 Logo" width="160" />

这个模板将 GitHub 仓库中的 Markdown 发布为文档站点，提供导航、搜索和主题切换。这份中文示例保存在文档源仓库 [nimbus-docs-template](https://github.com/Azincc/nimbus-docs-template.git) 的 `docs-zh-CN/` 目录中，同一仓库的 `docs/` 是默认英文示例。

## Python 基础复习

本仓库收录了一套系统的 Python 复习文档，共 13 章，覆盖从环境搭建、核心语法到面向对象与 asyncio 进阶内容：

- [Python 简介与特点](./python/introduction.md)：语言历史、核心特性与应用领域。
- [变量与数据类型](./python/data-types.md)：数字、字符串、列表、元组、字典、集合。
- [函数](./python/functions.md)：参数、lambda、作用域与 LEGB 规则。
- [面向对象编程基础](./python/oop.md)：类、继承、多态与魔术方法。
- [asyncio.eager_task_factory 详解](./python/async-eager-tasks.md)：Python 3.12 急切任务执行进阶专题。

完整章节见 [Python 基础复习目录](./python.md)。

## Agent 开发技能树

从基础到进阶的四层学习路径，系统覆盖构建 AI Agent 所需的全部知识点，含"要学什么"与"学习文档"：

- [01 基础层](./agent/basics.md)：Python 编程、LLM 原理、Prompt 工程。
- [02 核心层](./agent/core.md)：RAG 检索增强、函数调用 / 工具、记忆系统。
- [03 框架层](./agent/framework.md)：LangChain、Agent 框架、MCP 协议。
- [04 进阶层](./agent/advanced.md)：多 Agent 协作、评估与可观测、部署与运维。

完整目录见 [Agent 开发技能树总览](./agent.md)。

## 从这里开始

- [快速入门](./getting-started.md)：在 Cloudflare 部署网站并填写构建参数。
- [编写文档](./writing-docs.md)：添加页面、组织目录、引用图片和链接。
- [侧栏顺序](./sidebar-order.md)：设置页面、分类和分类内页面的排列顺序。
- [站点配置](./site-config.md)：修改站点名称、导航、主题和品牌资源。
- [修改部署配置](./deployment/configuration.md)：在 Cloudflare 填写文档源、站点地址、Logo 和浏览器图标，无需编写代码。
- [私有仓库部署](./deployment/private-repository.md)：创建只读 Token，通过 Cloudflare Build Secret 读取私有文档。
- [原文档仓库构建挂钩](./deployment/deploy-hook.md)：连接 GitHub Webhook 与 Cloudflare Deploy Hook，让文档推送自动触发构建。
- [更新模板](./deployment/template-update.md)：同步官方模板更新，也可将单独的操作说明交给 Agent 协助完成。
- [markdown显示测试](./markdown测试/markdown显示测试.md)：查看“markdown测试”分类中的常见 Markdown 样式。

## 示例如何工作

构建时，模板会拉取文档仓库，读取 `DOCS_PATH` 指定目录中的 Markdown，并生成静态页面、自动侧栏、页面目录和搜索索引。这份中文示例使用 `docs-zh-CN/` 目录，其中 `README.md` 对应首页，其他文件按路径生成页面。

这个首页没有 frontmatter，页面名称直接取自首个标题。其他示例页面用 frontmatter 指定标题、描述和侧栏顺序。页面间的 `.md` 相对链接和上方的本地 SVG 图片地址，都会在构建时转换为站点地址。

| 构建变量 | 模板默认值（英文） |
| --- | --- |
| `DOCS_REPO` | `https://github.com/Azincc/nimbus-docs-template.git` |
| `DOCS_BRANCH` | `main` |
| `DOCS_PATH` | `docs` |
| `DOCS_CONFIG_PATH` | `docs/site.json` |
| `SITE_URL` | `https://nimbus.az1n.com` |
| `SITE_LOGO` | `default`，沿用站点 JSON 的 `brand.logo` |
| `SITE_FAVICON` | `default`，沿用站点 JSON 的 `brand.favicon` |

默认配置读取英文 `docs/`，无需逐项填写这些变量。要发布这份完整中文示例，保留默认文档仓库和分支，只修改以下两个构建变量：

| 构建变量 | 中文示例值 |
| --- | --- |
| `DOCS_PATH` | `docs-zh-CN` |
| `DOCS_CONFIG_PATH` | `docs-zh-CN/site.json` |

保存并重新构建后，站点会显示中文文档。部署自己的站点时，将 `SITE_URL` 改为实际公开地址，也可以显式留空。设置这个变量不会自动绑定域名。

Logo 和 favicon 可以通过可选的构建变量设置。变量值接受 HTTP(S) 图片 URL，也接受相对文档源仓库根目录的路径，例如 `docs-zh-CN/assets/nimbus-mark.svg`。这类路径不受站点 JSON 位置影响；写在 JSON 中的资源路径仍相对 JSON 文件。

构建时先读取同名环境变量，未设置时使用 `wrangler.jsonc` 默认值。填图片 URL 或仓库路径时覆盖对应 JSON 品牌字段；默认的 `default` 或空白值沿用 JSON，未配置对应字段时使用模板内置的 Nimbus 官方 Logo。Cloudflare 不接受空值时，直接填 `default`。要修改这些变量，请打开 Cloudflare **Settings → Builds → Build variables and secrets**，添加普通变量后重新构建。运行时变量不会自动进入静态构建。详见[品牌资源](./site-config.md#品牌资源)。

旧部署请先[更新模板](./deployment/template-update.md)，同步新版构建脚本和 `public/nimbus-logo.svg`，再将变量设为 `default`。

## 内容版本

每次构建都会读取指定分支上的一个确定提交。页脚和 `/_build.json` 显示该文档提交的 SHA，可用来核对线上内容的版本。修改文档并推送后，重新触发构建即可发布更新。

这份中文文档源维护在 `docs-zh-CN/` 中；`src/content/docs/`、`public/_source/` 和 `dist/` 是构建生成目录。完整的部署及维护说明见 [仓库中文 README](https://github.com/Azincc/nimbus-docs-template/blob/main/README.zh-CN.md)。
