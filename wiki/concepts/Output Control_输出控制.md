---
title: Output Control（输出控制）
type: concept
tags: [ai, llm, prompt-engineering, structured-output]
sources:
  - "[2026-05-21-ai-harness-engineering-conversation](raw/clippings/2026-05-21-ai-harness-engineering-conversation.md)"
  - "[2026-05-21-spring-ai](raw/clippings/2026-05-21-spring-ai.md)"
---

# Output Control（输出控制）

输出控制是 [Prompt Engineering](Prompt%20Engineering.md) 中确保 AI 生成结果可预测、可解析、可复现的技术集合。

## 三种控制手段

- **[Structured Outputs](Structured%20Outputs.md)（结构化输出）**：引导模型返回 JSON、XML 等结构化格式而非自由文本，便于下游程序解析和处理。
- **约束解码（Constrained Decoding）**：在生成过程中强制输出匹配特定正则或语法规则（如 JSON Schema），保证输出的格式正确性。
- **温度与采样参数**：通过调整 `temperature`、`top_p`、`top_k` 等参数控制输出的随机性和创造性。

## Related

- [Prompt Engineering](Prompt%20Engineering.md) — 提示词工程整体方法论
- [Structured Outputs](Structured%20Outputs.md) — 结构化输出详解
- [基础提示技巧](Basic%20Prompting%20Techniques_基础提示技巧.md) — 基础交互模式
