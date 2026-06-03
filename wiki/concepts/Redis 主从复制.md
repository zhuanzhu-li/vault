---
title: Redis 主从复制
type: concept
tags: [redis, replication, master-slave, high-availability]
sources:
  - "[raw/clippings/2026-06-03-redis-knowledge-summary](raw/clippings/2026-06-03-redis-knowledge-summary)"
---

# Redis 主从复制

实现数据备份和读写分离。

## 同步方式

### 1. 完整重同步

处理初次复制：
1. 从服务器发送 `PSYNC ? -1`
2. 主服务器执行 BGSAVE 生成 RDB
3. 发送 RDB 文件和缓冲区命令

### 2. 部分重同步

处理断线后重复制：
1. 从服务器发送 `PSYNC run_id offset`
2. 主服务器检查偏移量是否在复制积压缓冲区
3. 发送增量命令

## 命令传播

同步后进入命令传播阶段：
- 主服务器将写命令发送给所有从服务器
- 从服务器每秒发送心跳检测

## 相关概念

- [[Redis 哨兵模式]]
- [[Redis 集群模式]]
- [[Redis]]
