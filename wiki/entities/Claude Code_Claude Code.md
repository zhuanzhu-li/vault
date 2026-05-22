---
title: Claude Code
type: entity
tags: [tools, ai-coding, anthropic]
aliases:
  - Claude Code
sources:
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
---

# Claude Code

Anthropic 的 AI 编码 CLI/IDE 工具。本文中的 **Harness** 即 Claude Code 宿主框架本身。

## 核心能力（本文语境）

- **Context 管理** — auto-compact（见 [上下文压缩](Context%20Compact_上下文压缩.md)）
- **Hooks** — `settings.json` 配置确定性自动化（见 [编码 Hooks](AI%20Coding%20Hooks_编码%20Hooks.md)）
- **Subagents** — 独立 context 的子代理（见 [Subagent 上下文隔离](Subagent%20Context%20Isolation_Subagent%20上下文隔离.md)）
- **Memory** — 跨会话项目记忆
- **SKILL / rules** — 可复用工作流与 path-scoped 规范

## 与 Obsidian 库的平行

本 vault 用 [黑曜石](Obsidian_黑曜石.md) + `AGENTS.md` + skills 实现类似「规则在文件、AI 按区维护」的模式，参见 [纯文本知识库](Plain%20Text%20Knowledge%20Base_纯文本知识库.md)。

## 相关

- [Harness 工程](Harness%20Engineering_Harness%20工程.md)
- [得物](Dewu_得物.md)
