---
title: Prompt Engineering（提示词工程）
type: entity
tags: [ai, llm, prompt-engineering, methodology]
sources:
  - "[[raw/clippings/2026-05-21-ai-harness-engineering-conversation]]"
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
  - "[[raw/clippings/2026-05-21-spring-ai]]"
---

# Prompt Engineering（提示词工程）

设计和优化输入提示词以引导大型语言模型（LLM）生成预期输出的方法论，是连接人类意图与模型能力的核心桥梁。

## 核心策略

- **[[Basic Prompting Techniques_基础提示技巧|基础提示技巧]]** — 零样本、少样本、角色提示、系统提示等基础交互模式
- **[[Chain of Thought]]（思维链）** — 引导模型逐步推理，提升复杂任务准确性；延伸策略包括思维树（ToT）、反思（Reflection）、ReAct
- **[[Output Control_输出控制|输出控制]]** — 结构化输出、约束解码、温度与采样参数，确保结果可预测可解析

## 在 AI 编码中的应用

在 [[Harness Engineering_Harness 工程|Harness 工程]] 实践中，提示词工程被系统化为明确的流程和约束：

- [[Process Harness]] — 将思维链编码为确定性步骤
- [[Context Compact_上下文压缩|上下文压缩]] — 管理长对话上下文，避免关键指令稀释
- [[AI Coding Hooks_编码 Hooks|编码 Hooks]] — 在推理循环外强制检查输出质量
- [[Semantic Data Accuracy_语义数据准确率|语义数据准确率]] — 衡量提示词约束被 AI 遵守的程度

## 常见陷阱

[[Prompt Pitfalls_提示词陷阱|提示词陷阱]] — 指令泄露、约束脆弱性、过度拟合示例、越狱攻击

## Related

- [[Basic Prompting Techniques_基础提示技巧|基础提示技巧]]
- [[Chain of Thought]]
- [[Output Control_输出控制|输出控制]]
- [[Prompt Pitfalls_提示词陷阱|提示词陷阱]]
- [[Structured Outputs]]
- [[Harness Engineering_Harness 工程|Harness 工程]]
- [[Process Harness]]
- [[Retrieval Augmented Generation]]
- [[AI Harness Engineering]]
