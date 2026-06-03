---
title: Redis
type: entity
tags: [database, nosql, cache, in-memory]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis

## 基本信息

- **全称**：Remote Dictionary Server
- **类型**：开源内存数据库
- **语言**：C语言
- **特性**：键值存储、持久化、高可用、分布式

## 核心优势

1. **高性能**：基于内存，单线程处理
2. **数据结构丰富**：string、hash、list、set、zset
3. **持久化**：RDB、AOF、混合持久化
4. **高可用**：主从复制、哨兵模式、集群模式
5. **扩展性**：支持集群横向扩展

## 应用场景

- 缓存系统
- 会话存储
- 实时排行榜
- 消息队列
- 计数器

## 相关概念

- [[Redis 数据类型]]
- [[Redis 持久化机制]]
- [[Redis 哨兵模式]]
- [[Redis 集群模式]]
