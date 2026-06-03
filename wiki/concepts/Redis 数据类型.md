---
title: Redis 数据类型
type: concept
tags: [redis, data-types, cache, nosql]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 数据类型

Redis 支持五种基本数据类型：string、hash、list、set、zset。

## 1. String

二进制安全的字符串类型，支持字符串、整数和浮点数。

**编码格式：**
- `int`：存储整数值
- `embstr`：短字符串（≤32字节）
- `raw`：长字符串（>32字节）

**应用场景：**
- 缓存：存储常用请求结果
- 计数器：实时计数
- 分布式锁

## 2. List

有序列表，可重复元素。

**编码格式：**
- `ziplist`：小列表
- `linkedlist`：大列表（字符串>64字节或元素>512个）

**应用场景：**
- 粉丝列表、好友列表
- 分页查询
- 阻塞队列

## 3. Set

无序列表，自动去重。

**编码格式：**
- `intset`：整数集合
- `hashtable`：大集合

**应用场景：**
- 共同好友、交集操作
- 去重

## 4. Hash

键值对集合，适合存储对象。

**编码格式：**
- `ziplist`：小哈希
- `hashtable`：大哈希

**应用场景：**
- 存储对象属性

## 5. Zset (Sorted Set)

有序集合，按分数排序。

**编码格式：**
- `ziplist`：小有序集合
- `skiplist`：大有序集合

**应用场景：**
- 排行榜
- 优先级队列

## 相关概念

- [[Redis]]
- [[Redis 持久化机制]]
