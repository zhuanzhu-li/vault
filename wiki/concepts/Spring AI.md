---
title: Spring AI
type: concept
tags: [ai-engineering, framework, java, spring]
sources:
 - "[[raw/clippings/2026-05-21-spring-ai]]"
---

# Spring AI

[[Spring AI]] 是 [[Spring Framework]] 生态系统中的一个用于 AI 工程的应用框架。其主要目标是将 Spring 的设计原则（如可移植性、模块化和 POJO）应用于 AI 领域。

它解决了 AI 集成的一个基本挑战：将企业**数据**和 **API** 与 **AI 模型**连接起来。

## 核心特性
- **便携式 API**：支持所有主流的 AI 模型提供商，如 [[OpenAI]]、Anthropic、微软、亚马逊、谷歌和 Ollama。
- **[[Structured Outputs]] (结构化输出)**：将 AI 模型输出映射为 POJO。
- **[[Vector Database]] (向量数据库) 支持**：通过便携式 API 和类似 SQL 的元数据过滤器连接各种向量存储。
- **[[ChatClient API]]**：用于与 AI 聊天模型通信的流畅 API。
- **[[Retrieval Augmented Generation]] (检索增强生成)**：内置对 RAG 和聊天对话内存的支持。

由 [[Broadcom]] 开发。
