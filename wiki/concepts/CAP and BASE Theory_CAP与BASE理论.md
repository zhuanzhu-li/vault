---
title: CAP and BASE Theory（CAP与BASE理论）
type: concept
tags: [distributed-systems, consistency, database, architecture]
sources: []
---

# CAP and BASE Theory（CAP与BASE理论）

CAP 定理和 BASE 理论是分布式系统设计的基石，它们描述了在分布式环境中一致性、可用性和分区容错性之间的权衡关系。

## CAP 定理

CAP 定理指出：一个分布式系统不可能同时满足以下三个特性：

| 特性 | 说明 |
|------|------|
| **Consistency（一致性）** | 所有节点在同一时刻看到相同的数据 |
| **Availability（可用性）** | 每个请求都能在合理时间内得到响应 |
| **Partition Tolerance（分区容错）** | 系统在网络分区发生时仍能运行 |

### CAP 三选二

| 选择 | 说明 | 应用场景 |
|------|------|---------|
| **CP** | 放弃可用性，保证一致性 | ZooKeeper、HBase、MongoDB |
| **AP** | 放弃强一致性，保证可用性 | Cassandra、DynamoDB、Eureka |
| **CA** | 理论上存在，但分布式系统必须容忍网络分区，实际不存在 | 单机数据库 |

### 常见误解

- CAP 不是三选一，而是在网络分区发生时选择 C 或 A
- 正常运行情况下，系统可以同时提供 C 和 A
- 分区恢复后，需要重新同步数据

## BASE 理论

BASE 是对 CAP 中一致性和可用性权衡的实践总结：

| 缩写 | 全称 | 说明 |
|------|------|------|
| **B** | Basically Available | 基本可用，允许系统在故障时降级 |
| **S** | Soft State | 软状态，数据状态可以暂时不一致 |
| **E** | Eventually Consistent | 最终一致性，数据最终会达到一致状态 |

### BASE vs ACID

| 维度 | ACID（传统数据库） | BASE（分布式系统） |
|------|-------------------|------------------|
| 一致性 | 强一致性 | 最终一致性 |
| 可用性 | 低 | 高 |
| 事务 | 严格的事务 | 柔性事务 |
| 典型场景 | 金融、订单 | 社交Feed、缓存 |

## 实践中的应用

### 选择 CP 还是 AP？

| 场景 | 推荐 | 原因 |
|------|------|------|
| 金融交易 | CP | 钱不能出错，必须强一致 |
| 社交Feed | AP | 用户体验优先，允许短暂不一致 |
| 配置中心 | CP | 配置错误影响大 |
| 商品库存 | CP | 超卖问题严重 |

### 实现最终一致性

| 方案 | 说明 |
|------|------|
| **定时对账** | 定期检查数据一致性 |
| **消息队列** | 异步重试保证最终一致 |
| **补偿事务** | 记录操作逆操作，失败时回滚 |
| **版本向量** | 多副本同步时跟踪版本 |

## 相关

- [Distributed Computing_分布式计算](Distributed%20Computing_分布式计算.md)
- [Apache HBase_Apache HBase](Apache%20HBase_Apache%20HBase.md)
- [PostgreSQL_PostgreSQL](PostgreSQL_PostgreSQL.md)
