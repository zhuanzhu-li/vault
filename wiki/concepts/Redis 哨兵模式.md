---
title: Redis 哨兵模式
type: concept
tags: [redis, sentinel, high-availability, failover]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 哨兵模式

保证 Redis 高可用，自动故障转移。

## 核心功能

1. **监控**：检查主从服务器状态
2. **通知**：向客户端发送故障通知
3. **故障转移**：主服务器下线时自动将从服务器升级为主服务器

## 故障检测

### 主观下线

单个哨兵判定主服务器下线。

### 客观下线

超过 quorum 数量的哨兵判定主服务器下线。

## 故障转移流程

1. 挑选新主服务器（优先级、偏移量、ID）
2. 其他从服务器复制新主服务器
3. 原主服务器上线后成为从服务器

## 相关概念

- [[Redis 主从复制]]
- [[Redis 集群模式]]
- [[Redis]]
