---
title: 数仓 AI 工作流
type: concept
tags: [ai-coding, data-warehouse, workflow, skill]
aliases:
  - DW AI Workflow
  - 数仓 AI 工作流
sources:
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
---

# 数仓 AI 工作流

[得物](Dewu_得物.md)离线数仓的 **8 步 SKILL 流程**，与 [Harness 工程](Harness%20Engineering_Harness%20工程.md) 三层机制（持久化 / Hooks / Subagent）对齐。

## 八步流程

| 步骤 | 名称 | 建议执行方式 |
|------|------|--------------|
| 1 | 需求分析 | 主会话 + dw-explorer 摘要 |
| 2 | 技术设计 | 主会话；结论写入 CLAUDE.md |
| 3 | ETL 开发 | PostToolUse hook + sql-validator |
| 4 | 自测 | data-quality-checker subagent |
| 5 | 数据比对 | data-comparator subagent |
| 6 | SR 导入 | dw-sr SKILL + 摘要 |
| 7 | 性能优化 | dw-explorer subagent |
| 8 | SLA/DQC | 主会话生成规则 JSON |

## 分工原则

- **主会话**：内容量有限、需人决策的步骤（需求、设计、DQC 规则）
- **Harness**：每次写 `.sql` 的规范检查（不依赖人工提醒）
- **Subagent**：血缘、自测、比对等会撑爆 context 的步骤

## SKILL 命令示例

`/dw-etl`、`/dw-自测` 等：封装规范 + 产出格式 + hook 护栏 + subagent 验证；主对话只收决策级信息。

## 相关

- [编码 Hooks](AI%20Coding%20Hooks_编码%20Hooks.md)
- [Subagent 上下文隔离](Subagent%20Context%20Isolation_Subagent%20上下文隔离.md)
- [语义数据准确率](Semantic%20Data%20Accuracy_语义数据准确率.md)
