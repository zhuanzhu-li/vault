---
title: Structured Outputs
type: concept
tags: [ai, data-engineering]
sources:
 - "[[raw/clippings/2026-05-21-spring-ai]]"
---

# 结构化输出 (Structured Outputs)

[[Structured Outputs]] 指的是 AI 模型以可预测的、强类型格式（如 JSON）而不是纯无结构文本返回数据的能力。

在 [[Spring AI]] 等框架中，结构化输出能够将 AI 模型的生成结果直接映射为 Plain Old Java Objects (POJO)，这使得将 AI 集成到传统软件工作流中变得更加容易，而无需脆弱的字符串解析。