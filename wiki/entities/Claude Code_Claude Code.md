---
title: Claude Code
type: entity
tags: [tools, ai-coding, anthropic]
aliases:
  - Claude Code
sources:
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
---

# Claude Code

Anthropic 的 AI 编码 CLI/IDE 工具。本文中的 **Harness** 即 Claude Code 宿主框架本身。

## 核心能力（本文语境）

- **Context 管理** — auto-compact（见 [[Context Compact_上下文压缩|上下文压缩]]）
- **Hooks** — `settings.json` 配置确定性自动化（见 [[AI Coding Hooks_编码 Hooks|编码 Hooks]]）
- **Subagents** — 独立 context 的子代理（见 [[Subagent Context Isolation_Subagent 上下文隔离|Subagent 上下文隔离]]）
- **Memory** — 跨会话项目记忆
- **SKILL / rules** — 可复用工作流与 path-scoped 规范

## 与 Obsidian 库的平行

本 vault 用 [[Obsidian_黑曜石|黑曜石]] + `AGENTS.md` + skills 实现类似「规则在文件、AI 按区维护」的模式，参见 [[Plain Text Knowledge Base_纯文本知识库|纯文本知识库]]。

## 相关

- [[Harness Engineering_Harness 工程|Harness 工程]]
- [[Dewu_得物|得物]]
