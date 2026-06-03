---
title: Redis 持久化机制
type: concept
tags: [redis, persistence, rdb, aof]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 持久化机制

Redis 提供三种持久化方式：RDB、AOF、混合持久化。

## 1. RDB

快照方式，保存某个时间点的数据集。

**触发方式：**
- `SAVE`：阻塞服务器
- `BGSAVE`：fork 子进程执行

**自动触发条件：**
- 900秒内至少1次写入
- 300秒内至少10次写入
- 60秒内至少10000次写入

**优点**：恢复速度快，文件体积小
**缺点**：可能丢失最后一次快照后的修改

## 2. AOF

记录所有写命令。

**同步策略：**
- `always`：每次写入都同步
- `everysec`：每秒同步（推荐）
- `no`：由操作系统决定

**AOF 重写**：压缩命令，避免文件膨胀

**优点**：数据安全性高
**缺点**：文件体积大，恢复速度慢

## 3. 混合持久化

AOF 重写时，先做 RDB 快照，再记录增量命令。

**优点**：兼顾 RDB 的恢复速度和 AOF 的数据安全性

## 相关概念

- [[Redis]]
- [[Redis 数据类型]]
