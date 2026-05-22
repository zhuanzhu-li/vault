---
title: Subagent 上下文隔离
type: concept
tags: [ai-coding, claude-code, context-window, subagent]
aliases:
  - Subagent Context Isolation
  - Subagent 上下文隔离
sources:
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
---

# Subagent 上下文隔离

将**高 token 消耗、但主会话只需摘要**的操作放到 [Claude Code](Claude%20Code_Claude%20Code.md) subagent 的独立 context 中执行，避免主对话被撑满并触发 [上下文压缩](Context%20Compact_上下文压缩.md)。

## 原则

主 context 只接收：PASS/FAIL、问题列表、表结构摘要（≤50~80 行）。  
不接收：23 条自测原始结果、完整 DDL、全量比对数据、SKILL 全文。

## 数仓典型 subagent

| Agent | 用途 |
|-------|------|
| sql-validator | SQL 语法与规范验证 |
| dw-explorer | 表结构、一层血缘探索 |
| data-quality-checker | 23 项标准自测 |
| data-comparator | 新旧表数据比对 |

## 与 SKILL 的配合

SKILL 文件不在主对话全文加载；由 subagent 内部读取 SKILL 执行，主会话只收产出路径与结论——配合 [数仓 AI 工作流](DW%20AI%20Workflow_数仓%20AI%20工作流.md)。

## 预期效果

主 context compact 频率预计降低 **50%~70%**（得物实践估算）。

## 相关

- [Harness 工程](Harness%20Engineering_Harness%20工程.md)
- [上下文压缩](Context%20Compact_上下文压缩.md)
