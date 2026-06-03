---
title: Redis 过期策略
type: concept
tags: [redis, expiration, memory-management]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 过期策略

Redis 支持三种过期策略来管理过期键。

## 三种策略

### 1. 定时删除

为设置过期时间的 key 添加定时器，到期立即删除。

**优点**：对内存最友好
**缺点**：对 CPU 不友好，影响响应时间

### 2. 定期删除

每隔一段时间（默认100ms），随机检查部分键的过期时间。

**优点**：平衡内存和 CPU
**缺点**：可能存在过期键未及时删除

### 3. 惰性删除

在访问键时才检查是否过期。

**优点**：CPU 友好
**缺点**：可能造成内存泄漏

## Redis 实际策略

Redis 使用**定期删除 + 惰性删除**相结合的策略：
- 默认每隔 100ms 随机抽查部分 key
- 访问时检查过期状态

## 相关概念

- [[Redis 内存淘汰策略]]
- [[Redis]]
