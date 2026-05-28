---
title: Distributed Lock（分布式锁）
type: concept
tags: [distributed-systems, lock, redis, architecture]
sources: []
---

# Distributed Lock（分布式锁）

分布式锁是在分布式环境中实现多进程/多节点互斥访问共享资源的技术。与单机锁不同，分布式锁需要考虑网络分区、节点故障等分布式场景。

## 分布式锁的特性

| 特性 | 说明 | 必须性 |
|------|------|--------|
| **互斥性** | 任意时刻只有一个客户端能持有锁 | 必须 |
| **可重入性** | 同一客户端可多次获取同一锁 | 推荐 |
| **死锁检测** | 持有锁的节点崩溃后能自动释放 | 必须 |
| **高性能** | 加锁/解锁操作延迟低 | 推荐 |
| **公平性** | 按请求顺序获取锁 | 可选 |

## Redis 分布式锁

### 最简单实现（不推荐生产使用）

```java
// SET key value NX PX milliseconds
// NX: 不存在时设置
// PX: 过期时间（毫秒）
String result = jedis.set(lockKey, requestId, "NX", "PX", expireTime);
if ("OK".equals(result)) {
    try {
        // 业务逻辑
    } finally {
        jedis.del(lockKey); // 释放锁
    }
}
```

### 正确的释放方式（Lua 脚本保证原子性）

```java
// Lua 脚本：只有持有者才能释放
String script = 
    "if redis.call('get', KEYS[1]) == ARGV[1] then " +
    "    return redis.call('del', KEYS[1]) " +
    "else " +
    "    return 0 " +
    "end";
    
 jedis.eval(script, 1, lockKey, requestId);
```

###存在的问题

| 问题 | 场景 | 解决方案 |
|------|------|---------|
| **单机故障** | Redis Master 宕机 | RedLock |
| **锁过期，业务未完成** | 业务执行时间 > 锁过期时间 | 续期机制 |
| **非原子性释放** | 判断和删除不是一条命令 | Lua 脚本 |

## RedLock（红锁）

### 原理

```
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Redis 1 │   │ Redis 2 │   │ Redis 3 │
│ (Master)│   │ (Master)│   │ (Master)│
└────┬────┘   └────┬────┘   └────┬────┘
     │              │              │
     └──────────────┼──────────────┘
                    ▼
            获取 N/2+1 个节点成功 = 获取锁成功
```

### 流程

1. 获取当前时间（毫秒）
2. 依次向 N 个 Redis 实例获取锁
3. 计算获取锁的耗时
4. 有效时间 = 原有效时间 - 耗时
5. 超过半数成功且有效时间 > 0 = 获取成功
6. 失败则向所有实例释放锁

### 争议

- 反对意见：RedLock 依赖同步时钟，不适合强一致性场景
- 支持意见：多机优于单机，适合高可用场景

## 锁续期（Watch Dog）

### 问题场景

```
客户端获取锁（30秒过期）
    ↓
开始执行业务（预计40秒）
    ↓
30秒后锁自动释放 ❌
    ↓
其他客户端获取锁，开始执行 ❌
    ↓
原客户端完成，想释放锁（已不是持有者）❌
```

### 解决方案

```
┌─────────────────────────────────┐
│           Watch Dog              │
│  每 10 秒检查是否持有锁          │
│  如果持有，续期 30 秒            │
│  业务完成，主动取消续期           │
└─────────────────────────────────┘
```

### Redisson 实现

```java
RLock lock = redisson.getLock("myLock");
lock.lock(); // 内部自动续期
try {
    // 业务逻辑
} finally {
    lock.unlock();
}
```

## ZooKeeper 分布式锁

### 临时顺序节点原理

```
/locks
  ├── lock-000000001  (客户端A创建)
  ├── lock-000000002  (客户端B创建)
  └── lock-000000003  (客户端C创建)

客户端A获取锁：检查自己是否是最小节点 → 是 → 持有锁
客户端B获取锁：检查自己是否是最小节点 → 否 → 等待
```

### Curator 实现

```java
InterProcessMutex mutex = new InterProcessMutex(client, "/locks/myLock");
mutex.acquire(30, TimeUnit.SECONDS);
try {
    // 业务逻辑
} finally {
    mutex.release();
}
```

### 对比 Redis

| 维度 | Redis 分布式锁 | ZooKeeper 分布式锁 |
|------|---------------|------------------|
| 性能 | 更高 | 较低 |
| 可靠性 | 依赖设计（RedLock 复杂） | 天然可靠 |
| 特性 | 可重入、Watch Dog | 临时节点、顺序节点 |
| 使用场景 | 追求性能 | 追求强可靠 |

## StampedLock（Java 8）

### 概念

| 模式 | 说明 |
|------|------|
| **写锁** | 独占，写时无读无写 |
| **悲观读锁** | 共享，读时无写 |
| **乐观读** | 无锁，性能最高 |

### 示例

```java
StampedLock sl = new StampedLock();

long stamp = sl.tryOptimisticRead(); // 乐观读
// 读取数据
if (!sl.validate(stamp)) { // 检查是否有写锁介入
    stamp = sl.readLock(); // 升级为悲观读
    try {
        // 重新读取数据
    } finally {
        sl.unlockRead(stamp);
    }
}
```

## 相关

- [Distributed Computing_分布式计算](Distributed%20Computing_分布式计算.md)
- [Redis](Redis)
- [CAP and BASE Theory_CAP与BASE理论](CAP%20and%20BASE%20Theory_CAP与BASE理论.md)
