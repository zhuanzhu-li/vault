---
title: Distributed ID Generation（分布式ID生成）
type: concept
tags: [distributed-systems, id-generation, architecture]
sources: []
---

# Distributed ID Generation（分布式ID生成）

在分布式系统中，需要为海量数据生成全局唯一标识符。分布式 ID 必须满足：全局唯一、有序性（可选）、高性能高可用、趋势递增。

## 常见方案对比

| 方案 | 结构 | 有序性 | 性能 | 依赖 |
|------|------|--------|------|------|
| **UUID** | 128位随机/命名 | 无 | 极高 | 无 |
| **Snowflake** | 时间戳+机器ID+序列号 | 是 | 高 | ZK/数据库 |
| **Leaf（号段）** | 区间分配 | 是 | 极高 | 数据库 |
| **ULID** | 时间戳+随机 | 是 | 高 | 无 |

## Snowflake 算法

### 核心结构

```
┌──────────────────────────────────────────────────────────────┐
│                      64 bit                                  │
├─────────────┬──────────────────┬────────────────┬────────────┤
│  符号位(1)  │    时间戳(41)      │   机器ID(10)   │ 序列号(12) │
└─────────────┴──────────────────┴────────────────┴────────────┘
```

| 字段 | 位数 | 说明 |
|------|------|------|
| 符号位 | 1 | 固定为 0 |
| 时间戳 | 41 | 毫秒时间戳，可用 69 年 |
| 机器ID | 10 | 可支持 1024 个节点 |
| 序列号 | 12 | 每毫秒最多 4096 个 ID |

### Java 实现要点

```java
public class SnowflakeIdGenerator {
    private final long epoch = 1609459200000L; // 2021-01-01
    private final long workerIdBits = 10L;
    private final long sequenceBits = 12L;
    
    public synchronized long nextId() {
        long timestamp = System.currentTimeMillis() - epoch;
        // 时间回拨处理：拒绝或等待
        if (timestamp < lastTimestamp) {
            throw new RuntimeException("Clock moved backwards");
        }
        if (timestamp == lastTimestamp) {
            sequence = (sequence + 1) & ((1 << sequenceBits) - 1);
            if (sequence == 0) {
                timestamp = waitNextMillis(timestamp);
            }
        } else {
            sequence = 0;
        }
        lastTimestamp = timestamp;
        return (timestamp << workerIdBits + sequenceBits) | (workerId << sequenceBits) | sequence;
    }
}
```

### 时间回拨处理

| 方案 | 说明 | 优缺点 |
|------|------|--------|
| **拒绝** | 检测到回拨直接抛异常 | 简单，但会丢失请求 |
| **等待** | 等待时间追上 | 保守，但可能阻塞 |
| **偏移量** | 记录上一次时间，后续累加 | 激进，可能产生重复 |

## Leaf（美团点评方案）

### 号段模式原理

```
┌─────────────┬─────────────────┬────────────────┐
│  biz_tag    │    max_id       │    step        │
├─────────────┼─────────────────┼────────────────┤
│  order      │    1000000      │    2000        │
└─────────────┴─────────────────┴────────────────┘
```

### 双 Buffer 优化

```
┌─────────────────────────────────────────────────┐
│              Buffer A (2000 IDs)                 │
│  [1000001, 1000002, ..., 1002000]             │
├─────────────────────────────────────────────────┤
│              Buffer B (预加载中)                 │
│              [                ]                │
└─────────────────────────────────────────────────┘
```

### 优势

- 性能极高：本地缓存，不依赖外部服务
- 天然分库键：不同业务用不同号段
- 容灾：数据库挂了，本地号段仍可用

## ULID（通用唯一词典编码）

```java
// 结构：时间戳(48bits) + 随机数(80bits)
// 时间戳部分保证有序
01ARZ3NDEKTSV4RRFFQ69G5FAV  →  完整 ULID
01ARZ3NDEK                     →  时间戳部分
```

## 选型建议

| 场景 | 推荐方案 | 原因 |
|------|---------|------|
| 订单号 | Snowflake | 有序、包含时间信息 |
| 消息ID | Snowflake | 高并发下仍有序 |
| 用户ID | 数据库自增 | 简单、连续 |
| 分布式缓存Key | UUID | 无中心、无依赖 |
| 事件溯源ID | ULID | 时间有序+随机唯一 |

## 相关

- [Distributed Computing_分布式计算](Distributed%20Computing_分布式计算.md)
- [MySQL Design Guidelines_MySQL设计规约](MySQL%20Design%20Guidelines_MySQL设计规约.md)
