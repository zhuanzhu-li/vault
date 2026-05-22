---
title: 上下文压缩
type: concept
tags: [ai-coding, claude-code, context-window]
aliases:
  - Context Compact
  - 上下文压缩
  - auto-compact
sources:
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
---

# 上下文压缩

[Claude Code](Claude%20Code_Claude%20Code.md) 的 **auto-compact** 机制：当对话 token 接近上限（约 **95%**）时，将历史替换为摘要，体积可降至约 **12%**。

## 会丢失什么

- 对话中临时口头约束（如「本次用 OVERWRITE」「amount 是千元」）
- SKILL 文件只读了一半的前几步内容
- 已注入的大量自测结果、血缘 DDL、数据比对样本

## 数仓场景的典型后果

- SQL 字段单位错误（千元当元 → 差 1000 倍）
- 重复询问已确认的需求口径
- 复杂需求越到后期 AI 越不可靠（越复杂越容易触发 compact）

## 应对策略

不依赖口头约束，而使用 [项目上下文持久化](Project%20Context%20Persistence_项目上下文持久化.md)（CLAUDE.md、Memory）与 [Subagent 上下文隔离](Subagent%20Context%20Isolation_Subagent%20上下文隔离.md)，由 [Harness 工程](Harness%20Engineering_Harness%20工程.md) 统筹。

## 相关

- [Harness 工程](Harness%20Engineering_Harness%20工程.md)
- [语义数据准确率](Semantic%20Data%20Accuracy_语义数据准确率.md)
