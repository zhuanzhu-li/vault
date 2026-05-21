---
title: 项目上下文持久化
type: concept
tags: [ai-coding, claude-code, persistence, knowledge-management]
aliases:
  - Project Context Persistence
  - 项目上下文持久化
sources:
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
---

# 项目上下文持久化

把迭代约束与全局规范写入磁盘，在 [[Claude Code_Claude Code|Claude Code]] 会话启动与 [[Context Compact_上下文压缩|compact]] 后自动重注入——解决「口头说了，compact 后全忘」。

## 两层机制

### CLAUDE.md（项目级）

路径：`.claude/CLAUDE.md`。建议结构：

- **正在开发** — 表名、版本、node_id、当前步骤
- **本次迭代约束** — 禁止修改的表、分区格式、字段单位
- **数仓全局规范** — 长期保留，≤100 行

操作：新迭代更新前两节；上线后清空「本次迭代约束」。

### Auto Memory（跨会话）

路径：`~/.claude/projects/<project>/memory/MEMORY.md`。主动让 Claude 记住踩坑（如「amount 是千元」「某字段特定场景为空」）。

## 与 [[Plain Text Knowledge Base_纯文本知识库|纯文本知识库]] 的类比

本 vault 的 `AGENTS.md` / `wiki/` 分工类似：规则与合成知识在文件中，而非锁在单次对话里——参见 [[File Over App_文件优于应用|文件优于应用]]。

## 相关

- [[Harness Engineering_Harness 工程|Harness 工程]]
- [[AI Coding Hooks_编码 Hooks|编码 Hooks]]（SessionStart compact 时重注入）
