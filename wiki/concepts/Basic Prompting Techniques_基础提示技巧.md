---
title: Basic Prompting Techniques（基础提示技巧）
type: concept
tags: [ai, llm, prompt-engineering]
sources:
  - "[2026-05-21-ai-harness-engineering-conversation](raw/clippings/2026-05-21-ai-harness-engineering-conversation.md)"
  - "[2026-05-20-claude-code-harness-dw-dewu](raw/clippings/2026-05-20-claude-code-harness-dw-dewu.md)"
  - "[2026-05-21-spring-ai](raw/clippings/2026-05-21-spring-ai.md)"
---

# Basic Prompting Techniques（基础提示技巧）

基础提示技巧是 [Prompt Engineering](Prompt%20Engineering.md) 中最底层、最通用的交互模式，不需要复杂推理链路即可生效。

## 四种基础模式

- **零样本提示（Zero-shot）**：直接给出指令，模型根据训练时获得的知识直接回答。
- **少样本提示（Few-shot）**：在提示中提供若干输入输出示例，引导模型模仿范例推理。
- **角色提示（Role Prompting）**：为模型分配角色（如"你是一名资深架构师"），利用角色知识约束输出风格和内容。
- **系统提示（System Prompt）**：在对话开始时定义模型的行为规范、约束条件和输出格式，对整个会话生效。

## Related

- [Prompt Engineering](Prompt%20Engineering.md) — 提示词工程整体方法论
- [Chain of Thought](Chain%20of%20Thought.md) — 进阶推理策略
- [输出控制](Output%20Control_输出控制.md) — 控制 AI 输出格式与随机性
