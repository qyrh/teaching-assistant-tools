# 教学助手 · 工具库

教学助手Next 左栏「🧰 工具」里浏览、下载的就是这个仓库。工具都是单文件（或单文件夹）的网页程序，下载到本地后**离线可跑**，不依赖安装包升级。

## 目录结构

```
catalog.json            工具清单（唯一的入口文件）
tools/
  <工具 id>/
    index.html          入口页（离线、自包含）
    cover.jpg           封面（可选，建议 960×540 JPEG）
    ...                 其它随工具分发的文件
README.md
```

## 加一个工具

1. 建目录 `tools/<id>/`，`id` 用小写字母、数字、连字符（如 `abacus-3d`）。
2. 把入口页放进去，命名 `index.html`；需要一起发的图片/字体也放这个目录。
   页面要能**双击打开就跑**：样式、脚本、依赖全部内联，不引用 CDN、不写死本机绝对路径。
3. 加一张封面 `cover.jpg`（960×540 左右，≤300 KB 为宜）。
4. 在 `catalog.json` 的 `tools` 数组里加一条，字段见下。
5. 提交推送。应用里点「刷新」即可看到。

## catalog.json 字段

顶层：

| 字段 | 说明 |
| --- | --- |
| `schema` | 清单格式版本，当前 `1` |
| `name` | 工具库名称，显示在面板标题旁 |
| `desc` | 一句话简介 |
| `updated` | 最后更新日期 `YYYY-MM-DD` |
| `categories` | 分类字典：`{ id, name, icon }`，面板用它做分类筛选 |
| `tools` | 工具数组 |

每个工具：

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `id` | 是 | 唯一标识，同时是本地目录名 |
| `name` | 是 | 显示名称 |
| `icon` | 否 | 一个 emoji，卡片左上角 |
| `version` | 是 | 版本号；已装版本不同则显示「可更新」 |
| `category` | 是 | 必须是 `categories` 里的 id |
| `tags` | 否 | 字符串数组，面板汇总成标签筛选条 |
| `tagline` | 是 | 一句话介绍（卡片副标题） |
| `desc` | 否 | 详细介绍（展开详情时显示） |
| `entry` | 是 | 入口文件名，通常 `index.html` |
| `cover` | 否 | 封面文件名，留空则用 `icon` 占位 |
| `files` | 是 | 需要下载的文件名数组（相对 `tools/<id>/`），含 `entry` 与 `cover` |
| `size` | 否 | 该工具全部文件字节数之和，面板显示体积 |
| `updated` | 否 | `YYYY-MM-DD` |
| `author` | 否 | 作者 |
| `featured` | 否 | `true` 时排在前面 |

## 应用怎么取

- 清单：`repos/qyrh/teaching-assistant-tools/contents/catalog.json?ref=main`（GitHub API，`Accept: application/vnd.github.raw`），失败回落 `raw.githubusercontent.com`，再失败用上次缓存。
- 文件：`repos/.../contents/tools/<id>/<file>?ref=main`，按 `files` 逐个下载。
- 本机目录：`<userData>/tools/<id>/`，安装时写入 `meta.json`（工具元数据 + 安装时间）。
- 源可在面板「源」里改：`github:owner/repo@branch` 或任意 `catalog.json` 直链。
