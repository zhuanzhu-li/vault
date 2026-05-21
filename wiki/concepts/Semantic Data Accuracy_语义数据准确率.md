---
title: 语义数据准确率
type: concept
tags: [ai-coding, data-warehouse, semantics, quality]
aliases:
  - Semantic Data Accuracy
  - 语义数据准确率
sources:
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
---

# 语义数据准确率

数仓 AI 开发中，瓶颈往往不是「会不会写 SQL」，而是**业务语义理解**（字段口径、履约定义、版本差异）。得物实践观察：需求理解偏差占返工 **40%~60%**。

## 公式

```
准确率 = 语义理解深度 × 数据规范覆盖度
```

| 因子 | 含义 | Harness 如何稳定 |
|------|------|------------------|
| 语义理解深度 | 口径、业务规则是否正确 | CLAUDE.md 迭代约束、Memory 踩坑、需求分析产出持久化 |
| 数据规范覆盖度 | SQL/建模规范是否执行 | [[AI Coding Hooks_编码 Hooks|编码 Hooks]] 强制检查 |

## 四类典型问题

1. **字段口径遗忘** — amount 千元当元 → CLAUDE.md + Memory
2. **需求理解偏差** — GMV 视角不一致 → 口径草稿 + Stop hook 完整性检查
3. **SQL 规范不一致** — 缺 PARTITION → PostToolUse hook
4. **context 耗尽** — 复杂宽表 → [[Subagent Context Isolation_Subagent 上下文隔离|Subagent 隔离]]

## 相关

- [[Harness Engineering_Harness 工程|Harness 工程]]
- [[DW AI Workflow_数仓 AI 工作流|数仓 AI 工作流]]
