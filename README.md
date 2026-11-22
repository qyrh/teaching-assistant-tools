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

- 清单：优先 `raw.githubusercontent.com/<owner>/<repo>/<branch>/catalog.json`；失败回落 GitHub contents API（`Accept: application/vnd.github.raw`）；再失败用上次缓存。
- 文件：同样**先 raw 后 API**，按 `files` 逐个下载。contents API 对大文件会截断（实测 709 KB 的文件只回 262 KB），所以 raw 是首选；下载后会核对 `Content-Length`，短了就换下一个源。
- 本机目录：`<userData>/tools/<id>/`，安装时写入 `meta.json`（工具元数据 + 安装时间）；封面缓存在 `<userData>/tools/covers/`。
- 更新：清单里 `version` 与本地 `meta.json` 里的不同，卡片就显示「更新到 vX」；点一下重下覆盖。
- 源可在面板「源」里改：`github:owner/repo@branch` 或任意 `catalog.json` 直链。

## 现有工具

| id | 名称 | 分类 | 版本 |
| --- | --- | --- | --- |
| `abacus-3d` | 电子计数器 | 数学 | 1.0.1 |
| `vertical-calc` | 竖式生成器 | 数学 | 1.0.0 |
| `square-packing` | 正方形装箱 | 数学 | 1.0.0 |
| `logic-exam` | 推理题生成器 | 思维 | 1.0.0 |
| `gcol-*`（61 个） | 教参游戏合集 | 教参游戏 | 1.0.0 |

## 教参游戏合集（gcol-*）

由「教学游戏工坊」产线自动发布：每个丛书系列一个合集工具（`A：小数同步` 按年级拆成 12 个），
`tools/<gcol-id>/` 下是 `index.html`（筛选页：年级/讲次/搜索）+ `g001.html…`（每个游戏一个文件，
安装后整包离线）。要点：

- **教参同键关联**：每个游戏的元数据（`教参`/`unit` 讲次/`grade` 年级/`series` 系列/`stage` 学段）
  与教学助手「教参体系」同一套键（系列+学段+【N】讲次），合集页卡片副标题即教参名，
  可直接在教学助手教参搜索里按名检索对应讲次备课。机器可读全量索引见仓库根 `games-index.json`
  （字段：gid/hub/file/name/jiaocan/src/unit/grade/stage/series）。
- **反馈闭环**：每个游戏右下角 💬 按钮 → 分类+描述 → 「提交」预填 GitHub issue
  （标题 `[反馈][<合集id>/gNNN v版本] 类别：摘要`）或「复制反馈内容」。工坊定时任务会拉取
  这类 issue、定位到具体游戏、排重做、重发新版合集并自动关闭 issue（版本号 patch+1，
  面板显示「更新到 vX」）。
- **自动更新**：`tool_publisher.py publish --incremental --push` 增量发布（源游戏变化→重注入→
  合集版本+1→push→自动 close 对应反馈 issue）。

