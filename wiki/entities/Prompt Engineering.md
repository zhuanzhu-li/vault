---
title: Prompt Engineering（提示词工程）
type: entity
tags: [ai, llm, prompt-engineering, methodology]
sources:
  - "[2026-05-21-ai-harness-engineering-conversation](raw/clippings/2026-05-21-ai-harness-engineering-conversation.md)"
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
  - "[2026-05-21-spring-ai](raw/clippings/2026-05-21-spring-ai.md)"
---

# Prompt Engineering（提示词工程）

设计和优化输入提示词以引导大型语言模型（LLM）生成预期输出的方法论，是连接人类意图与模型能力的核心桥梁。

## 核心策略

- **[基础提示技巧](Basic%20Prompting%20Techniques_基础提示技巧.md)** — 零样本、少样本、角色提示、系统提示等基础交互模式
- **[Chain of Thought](Chain%20of%20Thought.md)（思维链）** — 引导模型逐步推理，提升复杂任务准确性；延伸策略包括思维树（ToT）、反思（Reflection）、ReAct
- **[输出控制](Output%20Control_输出控制.md)** — 结构化输出、约束解码、温度与采样参数，确保结果可预测可解析

## 在 AI 编码中的应用

在 [Harness 工程](Harness%20Engineering_Harness%20工程.md) 实践中，提示词工程被系统化为明确的流程和约束：

- [Process Harness](Process%20Harness.md) — 将思维链编码为确定性步骤
- [上下文压缩](Context%20Compact_上下文压缩.md) — 管理长对话上下文，避免关键指令稀释
- [编码 Hooks](AI%20Coding%20Hooks_编码%20Hooks.md) — 在推理循环外强制检查输出质量
- [语义数据准确率](Semantic%20Data%20Accuracy_语义数据准确率.md) — 衡量提示词约束被 AI 遵守的程度

## 常见陷阱

[提示词陷阱](Prompt%20Pitfalls_提示词陷阱.md) — 指令泄露、约束脆弱性、过度拟合示例、越狱攻击

## Related

- [基础提示技巧](Basic%20Prompting%20Techniques_基础提示技巧.md)
- [Chain of Thought](Chain%20of%20Thought.md)
- [输出控制](Output%20Control_输出控制.md)
- [提示词陷阱](Prompt%20Pitfalls_提示词陷阱.md)
- [Structured Outputs](Structured%20Outputs.md)
- [Harness 工程](Harness%20Engineering_Harness%20工程.md)
- [Process Harness](Process%20Harness.md)
- [Retrieval Augmented Generation](Retrieval%20Augmented%20Generation.md)
- [AI Harness Engineering](AI%20Harness%20Engineering.md)
