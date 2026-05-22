---
title: Harness 工程
type: concept
tags: [ai-coding, claude-code, automation, data-warehouse]
aliases:
  - Harness Engineering
  - Harness 工程
  - Claude Code Harness
sources:
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
---

# Harness 工程

在 [Claude Code](Claude%20Code_Claude%20Code.md) 生态中，**Harness** 指宿主运行框架本身——管理 context 生命周期、确定性执行 [编码 Hooks](AI%20Coding%20Hooks_编码%20Hooks.md)、协调 [Subagent](Subagent%20Context%20Isolation_Subagent%20上下文隔离.md)，而非把自动化寄托于 LLM「记得去做」。

## 核心区分

| 组件 | 职责 |
|------|------|
| Claude（LLM） | 理解需求、设计方案、生成代码 |
| Harness | 规范检查、危险拦截、任务完整性验证 |
| Subagent | 大量读取、血缘、自测等重 token 操作 |
| CLAUDE.md + Memory | 跨会话字段口径与迭代约束 |

> Automated behaviors require hooks configured in settings.json — **the harness executes these, not Claude**.

## 与 [文件优于应用](File%20Over%20App_文件优于应用.md) 的关系

规范、迭代状态、SKILL 定义写在磁盘文件（`.claude/CLAUDE.md`、hooks 脚本、rules）中，compact 后可重注入——与「可控制的文件优于短暂的应用状态」一致，参见 [项目上下文持久化](Project%20Context%20Persistence_项目上下文持久化.md)。

## 数仓落地

[得物](Dewu_得物.md)离线数仓实践：三层架构（持久化 / Hooks / Subagent）配合 [数仓 AI 工作流](DW%20AI%20Workflow_数仓%20AI%20工作流.md) 8 步流程。

## 相关

- [上下文压缩](Context%20Compact_上下文压缩.md)
- [语义数据准确率](Semantic%20Data%20Accuracy_语义数据准确率.md)
