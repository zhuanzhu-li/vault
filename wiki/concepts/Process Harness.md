---
title: Process Harness
type: concept
tags: [ai-agent, software-engineering, workflow]
sources:
  - "[[raw/clippings/2026-05-21-ai-harness-engineering-conversation]]"
---

# Process Harness

Process Harness（研发流程驾驭）是 [[AI Harness Engineering]] 的另一核心维度。为了避免 AI 产生幻觉或直接编写低质量代码，通过工作流引擎强制约束 AI 先思考后执行。

其核心在于利用 [[Chain of Thought]]（思维链）将开发流程切分：
1. 需求解析与影响面分析
2. 存储与数据模型设计
3. API 契约与事务设计
4. 规范编码与自测清单

通过这一过程，人类开发者转变为代码 Reviewer 和架构师。