---
title: Coding Harness
type: concept
tags: [ai-agent, prompt-engineering, code-standards]
sources:
  - "[2026-05-21-ai-harness-engineering-conversation](raw/clippings/2026-05-21-ai-harness-engineering-conversation.md)"
---

# Coding Harness

Coding Harness（代码规范驾驭）是 [AI Harness Engineering](AI%20Harness%20Engineering.md) 的一个维度。它主要用于约束 AI 生成代码的风格、边界和安全红线。

主要作用包括：
- **实施强制代码标记**：建立可追溯性。
- **划定架构边界**：例如规定 Controller、Service、Mapper 的具体职责限制。
- **设定防御底线**：禁止 AI 擅自修改核心项目配置文件。

它常常被作为全局规则（例如 `.cursorrules` 文件）自动挂载到 AI 会话中。