---
title: Consistent Hashing（一致性哈希）
type: concept
tags: [distributed-systems, hashing, cache, architecture]
sources: []
---

# Consistent Hashing（一致性哈希）

一致性哈希是一种特殊的哈希算法，用于在分布式系统中实现数据分布的同时最小化节点增减时的数据迁移。

## 传统哈希的问题

### 取模哈希

```
node = hash(key) % N

节点数 N = 3
key = "user:100" → hash = 100 → node = 100 % 3 = 1
```

### 节点变更时的灾难

```
原来: N = 3,  key % 3 分布在 [0, 1, 2]
节点宕机: N = 2,  key % 2 分布在 [0, 1]
结果: 几乎所有 key 的分布都变了！❌
```

## 一致性哈希原理

### 哈希环

```
           0
           │
           ▼
    ┌──────────────────┐
    │                  │
    │    哈希环        │
    │   [0, 2^32)     │
    │                  │
    └──────────────────┘
           │
           ▼
        2^32-1
```

### 节点映射

```
key = "user:100" → hash = 100 → 顺时针找到第一个节点 → Node B

       hash=100
           │
           ▼
    ┌──────────────────┐
    │         ●Node A  │
    │    (hash=50)    │
    │                  │
    │              ●Node B    ← 命中
    │             (hash=150) │
    │                  │
    │                  │
    └──────────────────┘
```

### 节点变更时的影响

```
原状态: Node A, B, C 分布在环上
Key 落在: A→B 之间 → Node B

新增节点 D:
- D 在 A 和 B 之间
- 只有原本落在 A→D 之间的 key 需要迁移
- 其他 key 不受影响 ✓

移除节点 A:
- 只有原本落在 A→B 之间的 key 需要迁移到 B
- 其他 key 不受影响 ✓
```

## 虚拟节点

### 问题：节点数量少时分布不均匀

```
Node A: hash("NodeA")
Node B: hash("NodeB")
Node C: hash("NodeC")

如果 A 靠近 B，B 靠近 C，三个节点挤在一起
大量 key 都会落在 A-B 之间，B-C 之间很少
```

### 解决：引入虚拟节点

```
每个物理节点对应多个虚拟节点：
Node A → A1(hash), A2(hash), A3(hash), ...
Node B → B1(hash), B2(hash), B3(hash), ...
Node C → C1(hash), C2(hash), C3(hash), ...

虚拟节点越多，分布越均匀
通常每个物理节点 150-200 个虚拟节点
```

### 虚拟节点映射示例

```
真实节点: Node A (192.168.1.1)
虚拟节点:
  - Node A#1 → hash("192.168.1.1-1") = 50
  - Node A#2 → hash("192.168.1.1-2") = 150
  - Node A#3 → hash("192.168.1.1-3") = 250
```

## 应用场景

| 场景 | 说明 |
|------|------|
| **Memcached/Redis Cluster** | 客户端决定 key 落在哪个节点 |
| **Dubbo 负载均衡** | 服务提供者列表的负载分配 |
| **Nginx 负载均衡** | upstream 服务的健康检查与路由 |
| **分布式文件系统** | 数据块的存储节点分配 |

## Java 实现示例

```java
public class ConsistentHash<T> {
    private final TreeMap<Long, T> circle = new TreeMap<>();
    private final HashFunction hashFunction;
    private final int virtualNodes;
    
    public ConsistentHash(HashFunction hashFunction, int virtualNodes) {
        this.hashFunction = hashFunction;
        this.virtualNodes = virtualNodes;
    }
    
    public void addNode(T node) {
        // 添加物理节点和虚拟节点
        for (int i = 0; i < virtualNodes; i++) {
            long hash = hashFunction.hash(node.toString() + "-" + i);
            circle.put(hash, node);
        }
    }
    
    public T getNode(String key) {
        if (circle.isEmpty()) {
            return null;
        }
        long hash = hashFunction.hash(key);
        // 找到第一个大于等于 hash 的节点
        Map.Entry<Long, T> entry = circle.ceilingEntry(hash);
        // 如果没有找到，顺时针回到第一个节点
        if (entry == null) {
            entry = circle.firstEntry();
        }
        return entry.getValue();
    }
}
```

## 相关

- [Distributed Computing_分布式计算](Distributed%20Computing_分布式计算.md)
- [Distributed Lock_分布式锁](Distributed%20Lock_分布式锁.md)
