---
title: Prompt Pitfalls（提示词陷阱）
type: concept
tags: [ai, llm, prompt-engineering, security]
sources:
  - "[[raw/clippings/2026-05-21-ai-harness-engineering-conversation]]"
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
---

# Prompt Pitfalls（提示词陷阱）

[[Prompt Engineering]] 实践中常见的失败模式和安全风险。

## 四大陷阱

- **指令泄露（Prompt Leaking）**：提示词中的敏感规则可能被模型在输出中泄露。
- **约束脆弱性**：口头约束（"记住用 OVERWRITE 模式"）容易在长对话中被 [[Context Compact_上下文压缩|上下文压缩]] 机制丢失。
- **过度拟合示例**：少样本提示中的示例如果太少或太特定，模型可能过度模仿而非理解泛化规则。
- **越狱攻击（Jailbreaking）**：恶意用户通过精心构造的提示词绕过模型的安全护栏。

## Related

- [[Prompt Engineering]] — 提示词工程整体方法论
- [[Basic Prompting Techniques_基础提示技巧|基础提示技巧]] — 基础交互模式
- [[Context Compact_上下文压缩|上下文压缩]] — 约束脆弱性的根因
