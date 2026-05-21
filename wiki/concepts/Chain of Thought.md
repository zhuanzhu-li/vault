---
title: Chain of Thought
type: concept
tags: [ai-agent, prompt-engineering, logic]
sources:
  - "[[raw/clippings/2026-05-21-ai-harness-engineering-conversation]]"
---

# Chain of Thought (思维链)

Chain of Thought (CoT) 是一种提示工程策略，旨在引导大型语言模型在给出最终答案之前，先生成中间推理步骤。

在 [[Process Harness]] 实践中，思维链被用来强制 AI 在编写代码前，逐步分析现有系统、设计数据模型、规划接口和事务边界，从而显著降低幻觉并提高输出质量。