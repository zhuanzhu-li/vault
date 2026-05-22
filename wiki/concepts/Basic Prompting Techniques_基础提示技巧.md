---
title: Basic Prompting Techniques（基础提示技巧）
type: concept
tags: [ai, llm, prompt-engineering]
sources:
  - "[[raw/clippings/2026-05-21-ai-harness-engineering-conversation]]"
  - "[[raw/clippings/2026-05-20-claude-code-harness-dw-dewu]]"
  - "[[raw/clippings/2026-05-21-spring-ai]]"
---

# Basic Prompting Techniques（基础提示技巧）

基础提示技巧是 [[Prompt Engineering]] 中最底层、最通用的交互模式，不需要复杂推理链路即可生效。

## 四种基础模式

- **零样本提示（Zero-shot）**：直接给出指令，模型根据训练时获得的知识直接回答。
- **少样本提示（Few-shot）**：在提示中提供若干输入输出示例，引导模型模仿范例推理。
- **角色提示（Role Prompting）**：为模型分配角色（如"你是一名资深架构师"），利用角色知识约束输出风格和内容。
- **系统提示（System Prompt）**：在对话开始时定义模型的行为规范、约束条件和输出格式，对整个会话生效。

## Related

- [[Prompt Engineering]] — 提示词工程整体方法论
- [[Chain of Thought]] — 进阶推理策略
- [[Output Control_输出控制|输出控制]] — 控制 AI 输出格式与随机性
