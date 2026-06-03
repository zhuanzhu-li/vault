---
title: Redis 集群模式
type: concept
tags: [redis, cluster, distributed, scaling]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 集群模式

实现分布式存储，解决在线扩容问题。

## 架构特点

- 所有节点彼此互联
- 16384 个槽位分布在不同节点
- 客户端直连节点，无需代理

## 槽位分配

使用 CRC16 算法计算键所属槽位：
```
slot = CRC16(key) & 16383
```

## 故障检测与转移

- 节点定期发送 PING/PONG
- 超过半数主节点标记下线则故障
- 从节点投票升级为主节点

## 相关概念

- [[Redis 哨兵模式]]
- [[Redis 主从复制]]
- [[Redis]]
