---
title: Redis 内存淘汰策略
type: concept
tags: [redis, memory, eviction, lru, lfu]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 内存淘汰策略

当 Redis 内存达到上限时，根据配置的策略淘汰部分 key。

## 淘汰策略列表

| 策略 | 说明 |
|------|------|
| `noeviction` | 默认策略，不淘汰，返回错误 |
| `allkeys-lru` | 在所有 key 中使用 LRU 算法 |
| `allkeys-lfu` | 在所有 key 中使用 LFU 算法 |
| `allkeys-random` | 随机淘汰 |
| `volatile-lru` | 在设置过期时间的 key 中使用 LRU |
| `volatile-lfu` | 在设置过期时间的 key 中使用 LFU |
| `volatile-random` | 在设置过期时间的 key 中随机淘汰 |
| `volatile-ttl` | 淘汰 TTL 最短的 key |

## LRU 算法实现

1. 随机抽样选出最多 N 个数据放入待淘汰池
2. 再次抽样，替换池中 LRU 最大的数据
3. 淘汰池中 LRU 最小的数据

## LFU vs LRU

- **LRU**：最近最少使用
- **LFU**：最不经常使用

## 相关概念

- [[Redis 过期策略]]
- [[Redis]]
