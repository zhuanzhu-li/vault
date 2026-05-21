---
title: Harness 工程
type: concept
tags: [ai-coding, claude-code, automation, data-warehouse]
aliases:
  - Harness Engineering
  - Harness 工程
  - Claude Code Harness
sources:
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
---

# Harness 工程

在 [[Claude Code_Claude Code|Claude Code]] 生态中，**Harness** 指宿主运行框架本身——管理 context 生命周期、确定性执行 [[AI Coding Hooks_编码 Hooks|编码 Hooks]]、协调 [[Subagent Context Isolation_Subagent 上下文隔离|Subagent]]，而非把自动化寄托于 LLM「记得去做」。

## 核心区分

| 组件 | 职责 |
|------|------|
| Claude（LLM） | 理解需求、设计方案、生成代码 |
| Harness | 规范检查、危险拦截、任务完整性验证 |
| Subagent | 大量读取、血缘、自测等重 token 操作 |
| CLAUDE.md + Memory | 跨会话字段口径与迭代约束 |

> Automated behaviors require hooks configured in settings.json — **the harness executes these, not Claude**.

## 与 [[File Over App_文件优于应用|文件优于应用]] 的关系

规范、迭代状态、SKILL 定义写在磁盘文件（`.claude/CLAUDE.md`、hooks 脚本、rules）中，compact 后可重注入——与「可控制的文件优于短暂的应用状态」一致，参见 [[Project Context Persistence_项目上下文持久化|项目上下文持久化]]。

## 数仓落地

[[Dewu_得物|得物]]离线数仓实践：三层架构（持久化 / Hooks / Subagent）配合 [[DW AI Workflow_数仓 AI 工作流|数仓 AI 工作流]] 8 步流程。

## 相关

- [[Context Compact_上下文压缩|上下文压缩]]
- [[Semantic Data Accuracy_语义数据准确率|语义数据准确率]]
