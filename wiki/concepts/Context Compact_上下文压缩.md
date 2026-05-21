---
title: 上下文压缩
type: concept
tags: [ai-coding, claude-code, context-window]
aliases:
  - Context Compact
  - 上下文压缩
  - auto-compact
sources:
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
---

# 上下文压缩

[[Claude Code_Claude Code|Claude Code]] 的 **auto-compact** 机制：当对话 token 接近上限（约 **95%**）时，将历史替换为摘要，体积可降至约 **12%**。

## 会丢失什么

- 对话中临时口头约束（如「本次用 OVERWRITE」「amount 是千元」）
- SKILL 文件只读了一半的前几步内容
- 已注入的大量自测结果、血缘 DDL、数据比对样本

## 数仓场景的典型后果

- SQL 字段单位错误（千元当元 → 差 1000 倍）
- 重复询问已确认的需求口径
- 复杂需求越到后期 AI 越不可靠（越复杂越容易触发 compact）

## 应对策略

不依赖口头约束，而使用 [[Project Context Persistence_项目上下文持久化|项目上下文持久化]]（CLAUDE.md、Memory）与 [[Subagent Context Isolation_Subagent 上下文隔离|Subagent 上下文隔离]]，由 [[Harness Engineering_Harness 工程|Harness 工程]] 统筹。

## 相关

- [[Harness Engineering_Harness 工程|Harness 工程]]
- [[Semantic Data Accuracy_语义数据准确率|语义数据准确率]]
