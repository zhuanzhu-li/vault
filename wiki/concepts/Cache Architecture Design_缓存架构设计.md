---
title: Cache Architecture Design（缓存架构设计）
type: concept
tags: [distributed-systems, cache, redis, architecture, performance]
sources: []
---

# Cache Architecture Design（缓存架构设计）

缓存是提升系统性能的核心组件，用于存储热点数据，减少数据库压力。缓存架构设计需要考虑命中率、一致性、容量规划和高可用。

## 缓存分类

| 分类 | 说明 | 示例 |
|------|------|------|
| **本地缓存** | 单机内存，速度快 | Caffeine、Guava Cache |
| **分布式缓存** | 多机共享 | Redis、Memcached |
| **多级缓存** | 本地 + 分布式组合 | L1 (本地) + L2 (Redis) |

## 缓存读写模式

### Cache Aside（旁路缓存）

```
读操作：
1. 读缓存 → 命中 → 返回
2. 读缓存 → 未命中 → 读数据库 → 写缓存 → 返回

写操作：
1. 写数据库
2. 删除缓存（不是更新！）
```

### 适合场景

- 读多写少的数据
- 数据一致性要求不高

### 为什么是删除而不是更新？

```
问题：更新缓存 + 写数据库

Thread A: 更新缓存 (name="B")
Thread B: 读取缓存 → 未命中 → 读DB (name="A")
Thread A: 写数据库 (name="B")

结果：缓存是 B，数据库是 A，数据不一致！
```

### Read Through / Write Through

| 模式 | 说明 |
|------|------|
| **Read Through** | 缓存负责从数据库加载数据，应用只和缓存交互 |
| **Write Through** | 写操作同时更新缓存和数据库 |

## 缓存问题与解决方案

### 缓存穿透

**定义**：查询不存在的数据，每次都打到数据库

```
攻击特征：大量无效 key 查询，如 id=-1
```

| 方案 | 说明 |
|------|------|
| **布隆过滤器** | 用少量内存标记存在或不存在的 key |
| **缓存空值** | 对查询结果为空的也缓存，设置短过期时间 |
| **参数校验** | 接口层拦截无效参数 |

### 缓存击穿

**定义**：热点 key 过期瞬间，大量请求打到数据库

```
热点 key 设置相同过期时间 → 同时过期 → 数据库被打爆
```

| 方案 | 说明 |
|------|------|
| **互斥锁** | 只允许一个线程重建缓存 |
| **热点数据永不过期** | 逻辑过期，不设置 TTL |
| **随机 TTL** | 不同实例的同一 key 设置不同过期时间 |

### 缓存雪崩

**定义**：大量 key 同时过期，或缓存服务宕机

```
场景1: 缓存服务宕机 → 所有请求打到数据库
场景2: 大促期间大量 key 集中过期
```

| 方案 | 说明 |
|------|------|
| **Redis 高可用** | 主从 + 哨兵 / Cluster |
| **本地缓存兜底** | Redis 不可用时用本地缓存 |
| **熔断降级** | Hystrix/Sentinel 保护数据库 |
| **过期时间随机** | 不同 key 设置不同 TTL |
| **预热** | 系统启动时加载热点数据 |

## Redis 数据结构选择

| 数据结构 | 使用场景 | 内存占用 | 复杂度 |
|---------|---------|---------|--------|
| **String** | 简单值、序列化对象 | 低 | O(1) |
| **Hash** | 对象、字段级操作 | 中 | O(1) |
| **List** | 队列、最新N条 | 低 | O(1) |
| **Set** | 去重、标签、好友 | 中 | O(1) |
| **Sorted Set** | 排行榜、延迟队列 | 高 | O(log N) |
| **Bitmap** | 签到、去重标记 | 极低 | O(1) |
| **HyperLogLog** | UV 统计 | 极低 | O(1) |

## Redis 内存淘汰策略

| 策略 | 说明 |
|------|------|
| **noeviction** | 不淘汰，返回错误 |
| **volatile-lru** | 从已设置过期时间的 key 中淘汰最近最少使用 |
| **allkeys-lru** | 从所有 key 中淘汰最近最少使用 |
| **volatile-random** | 从已设置过期时间的 key 中随机淘汰 |
| **allkeys-random** | 从所有 key 中随机淘汰 |
| **volatile-ttl** | 淘汰即将过期的 key |
| **volatile-lfu** | 从已设置过期时间的 key 中淘汰最不常用 |
| **allkeys-lfu** | 从所有 key 中淘汰最不常用 |

## 大 Key 问题

### 识别

```bash
# 查看大 key
redis-cli --bigkeys
redis-cli --scan-pattern "*" | head -100 | xargs redis-cli --big

# 分析 key 大小
redis-cli MEMORY USAGE user:1000000
```

### 解决

| 方案 | 说明 |
|------|------|
| **拆分** | 大 Hash 拆分为多个小 Hash |
| **压缩** | JSON 用 gzip 压缩后存储 |
| **删除** | 分批删除，避免阻塞 |

```java
// 分批删除大 key
public void deleteBigKey(String key) {
    Long size = redisTemplate.opsForSet().size(key);
    long batchSize = 1000;
    for (long i = 0; i < size; i += batchSize) {
        redisTemplate.opsForSet().remove(key, 
            redisTemplate.opsForSet().pop(key, batchSize).toArray());
    }
    redisTemplate.delete(key);
}
```

## 缓存容量规划

### 计算公式

```
所需内存 = key数量 × 平均key大小 × 冗余系数
```

### 估算示例

```
场景：1亿用户，每用户100个key，每个key 100字节
内存 = 1e8 × 100 × 100 bytes × 1.5(冗余)
     = 15 TB

建议：使用 Redis Cluster 至少 8 节点，每节点 256GB
```

## 多级缓存架构

```
┌─────────────────────────────────────────────────┐
│                    应用层                        │
│  ┌─────────────┐     ┌──────────────────────┐   │
│  │  LocalCache │ ──▶ │    RemoteCache      │   │
│  │ (Caffeine)  │     │     (Redis)         │   │
│  │  L1 100MB  │     │     L2 64GB        │   │
│  └─────────────┘     └──────────────────────┘   │
└─────────────────────────────────────────────────┘
```

### Caffeine 配置

```java
Cache<String, Object> localCache = Caffeine.newBuilder()
    .maximumSize(10000)        // 最大条目数
    .expireAfterWrite(60, TimeUnit.SECONDS)  // 写入后60秒过期
    .softValues()              // 内存不足时回收
    .recordStats()             // 开启统计
    .build();
```

## 相关

- [Distributed Lock_分布式锁](Distributed%20Lock_分布式锁.md)
- [Redis](Redis)
- [Interface Idempotency Design_接口幂等设计](Interface%20Idempotency%20Design_接口幂等设计.md)
