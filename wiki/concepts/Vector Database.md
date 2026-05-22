---
title: Vector Database
type: concept
tags: [database, ai, search]
sources:
 - "[2026-05-21-spring-ai](raw/clippings/2026-05-21-spring-ai.md)"
---

# 向量数据库 (Vector Database)

[Vector Database](Vector%20Database.md)（或称向量存储）是一种专门设计用于高效存储和查询高维向量嵌入的数据库。它们对于在 AI 应用程序中实现 [Retrieval Augmented Generation](Retrieval%20Augmented%20Generation.md) 等模式至关重要。

主要提供商包括 Apache Cassandra、Azure Vector Search、Chroma、Milvus、Neo4j、PostgreSQL/PGVector、PineCone、Redis 和 Qdrant。

在 [Spring AI](Spring%20AI.md) 中，这些数据库通过便携式 API 得到支持，允许开发人员在不更改应用程序逻辑的情况下在不同的向量数据库之间切换，并支持类似 SQL 的元数据过滤。
