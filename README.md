# 个人知识库（Obsidian Vault）

基于 **文件优于应用**（[File over app](https://stephango.com/file-over-app)）理念构建的 Obsidian 知识库：原始资料由你策展，百科由 AI 维护，工作笔记人机协作。全部内容以 Markdown 文件存储，可用任意编辑器阅读。

**入口：** 在 Obsidian 中打开 [[Wiki Index_维基索引|维基索引]]。

---

## 目录结构

```
vault/
├── raw/                    # 区 1：原始资料（只读）
│   ├── clippings/           #   网页剪藏
│   ├── papers/             #   论文 PDF / 阅读笔记（待建）
│   ├── books/              #   书籍（待建）
│   └── daily/              #   日记、闪念（待建）
│
├── wiki/                   # 区 2：百科（AI 维护）
│   ├── concepts/           #   概念页
│   ├── entities/           #   实体页（人物、公司、工具）
│   └── Wiki Index_维基索引.md
│
├── dev/                    # 区 3：工作笔记（人机协作，待建）
│   ├── adr/                #   架构决策记录
│   ├── debriefs/           #   事故复盘
│   └── projects/           #   项目笔记
│
├── .agents/skills/         # Agent 技能（defuddle、Obsidian 语法等）
├── .claude/commands/       # Cursor / Claude 斜杠命令（wiki-ingest 等）
├── AGENTS.md               # AI 行为规则（三区权限、导入流程）
├── skills-lock.json        # 技能版本锁定
└── README.md               # 本说明书
```

`.obsidian/`、`.claude/skills/` 为本地或工具配置，已在 `.gitignore` 中忽略，不纳入版本库。

---

## 三区说明

| 区域 | 路径 | 谁维护 | 规则摘要 |
|------|------|--------|----------|
| **原始资料** | `raw/` | 你 | Agent **不得**编辑、重命名或移动；仅可阅读并通过 `[[wikilinks]]` 引用 |
| **百科** | `wiki/` | AI | 概念提炼、实体卡片、索引；你很少手改，有变更需求可让 AI 重新生成 |
| **工作区** | `dev/` | 人机协作 | ADR、复盘、项目片段；**修改已有 ADR 前须你明确确认** |

### `raw/` — 原始资料

存放你主动收集的一手来源：

- **剪藏** `raw/clippings/YYYY-MM-DD-slug.md`：文章全文 + 元数据（`source-url`、`captured-date`、`title`、`author`）
- 后续可扩展：`papers/`、`books/`、`daily/` 等

剪藏文末通常附有 **百科链接**，指向 `wiki/` 中对应概念与实体。

### `wiki/` — 百科

从 `raw/` 蒸馏出的结构化知识：

| 子目录 | `type` | 内容示例 |
|--------|--------|----------|
| `concepts/` | `concept` | 文件优于应用、数字耐久性 |
| `entities/` | `entity` | 斯蒂芬·安戈、黑曜石（Obsidian） |
| 根目录 | `synthesis` | [[Wiki Index_维基索引\|维基索引]] |

每页必须包含 frontmatter（`title`、`type`、`tags`、`sources`），且正文至少有一个指向其他页面的 wikilink。

### `dev/` — 工作笔记

技术产出与协作区（目录可按项目逐步创建）：

- `adr/` — 架构决策
- `debriefs/` — 事故复盘（含 `incident-date`、`severity`）
- `projects/` — 项目笔记

---

## 文件命名约定

百科页采用 **`English_中文.md`** 双语文件名，兼顾排序、检索与中文阅读：

```
wiki/concepts/File Over App_文件优于应用.md
wiki/entities/Steph Ango_斯蒂芬·安戈.md
wiki/Wiki Index_维基索引.md
```

| 部分 | 说明 |
|------|------|
| `English` | 英文标识，便于脚本与国际化 |
| `_` | 分隔符 |
| `中文` | 中文别名，与 `title` 一致 |

**链接写法：**

```markdown
[[File Over App_文件优于应用|文件优于应用]]
```

- 完整路径保证链接稳定
- `|中文` 控制显示文字
- frontmatter 中的 `aliases` 支持简写 `[[文件优于应用]]`

**剪藏文件** 使用日期 + 英文 slug：

```
raw/clippings/2026-05-20-file-over-app.md
```

---

## Frontmatter 模板

### 百科页（concept / entity / synthesis）

```yaml
---
title: 文件优于应用
type: concept          # concept | entity | synthesis
tags: [knowledge-management, data-ownership]
aliases:
  - File Over App
  - 文件优于应用
sources:
  - "[[raw/clippings/2026-05-20-file-over-app]]"
---
```

### 剪藏页

```yaml
---
source-url: https://example.com/article
captured-date: 2026-05-20
title: 文章中文标题
original-title: Original English Title   # 可选
author: Author Name
published: 2023-07-01                    # 可选
---
```

### 工作区（示例：debrief）

```yaml
---
title: 某次 outage 复盘
type: debrief
tags: [incident, api]
incident-date: 2026-05-01
severity: high
sources: []
---
```

`tags` 使用 kebab-case。内部链接一律用 `[[wikilinks]]`，不要用 `[text](path.md)`。

---

## 导入流程（`/wiki-ingest`）

将 URL 或本地文件导入库并生成百科：

1. **提取** — URL 用 `defuddle` 技能；本地文件直接读取
2. **保存剪藏** — `raw/clippings/YYYY-MM-DD-slug.md`
3. **分析** — 识别 3–7 个概念、1–3 个实体
4. **计划** — 影响超过 5 个文件时先展示计划、等待批准
5. **写入百科** — 新建或更新 `wiki/concepts/`、`wiki/entities/`
6. **链接** — 剪藏 ↔ 百科双向 wikilink
7. **更新索引** — [[Wiki Index_维基索引|维基索引]]
8. **报告** — 列出新建/修改的文件

命令定义见 `.claude/commands/wiki-ingest.md`。

---

## AI 与工具

| 路径 | 用途 |
|------|------|
| `AGENTS.md` | Agent 会话规则：三区权限、导入流程、安全约束 |
| `.agents/skills/` | 领域技能（网页提取、Obsidian Markdown、ADR 写作等） |
| `.claude/commands/` | `wiki-ingest`、`wiki-query` 等斜杠命令 |

**安全约定（摘要）：**

- 不删除文件（除非你明确确认）
- 不自动 `git push`
- 不修改 `AGENTS.md`（除非你要求）
- `raw/` 对 Agent 只读

---

## 当前内容快照

**已导入剪藏：**

- [[raw/clippings/2026-05-20-file-over-app|2026-05-20-file-over-app]] — *文件优于应用*（Steph Ango，2023）

**百科概念（6）：** 文件优于应用、数字耐久性、软件易逝性、用户数据所有权、纯文本知识库、开放文件格式

**百科实体（2）：** 斯蒂芬·安戈、黑曜石（Obsidian）

完整列表见 [[Wiki Index_维基索引|维基索引]]。

---

## 快速开始

1. 用 **Obsidian** 打开本文件夹作为库
2. 从 [[Wiki Index_维基索引|维基索引]] 或图谱浏览已有笔记
3. 阅读 `raw/clippings/` 中的剪藏原文
4. 在 Cursor 中执行 `/wiki-ingest <URL>` 导入新文章
5. 工作相关笔记写入 `dev/`（与 AI 协作时注意 ADR 修改须先确认）

---

## 版本管理

```bash
git status
git add <paths>
git commit -m "feat(wiki): ..."
# push 由你手动执行
```

Obsidian 工作区状态（`workspace.json` 等）已 gitignore，避免个人窗口布局污染仓库。
