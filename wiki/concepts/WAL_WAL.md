---
title: WAL（预写日志）
type: concept
tags: [database, transaction, durability, postgresql]
sources:
  - "[2026-05-22-postgresql-deep-dive](raw/clippings/2026-05-22-postgresql-deep-dive.md)"
---

# WAL（Write-Ahead Logging）

WAL（预写日志）是数据库系统保证事务持久性和崩溃恢复的核心机制。核心理念：**在数据写入磁盘之前，先将日志写入 WAL 日志文件**。

## 工作原理

1. 事务修改数据时，先将变更记录写入 WAL 缓冲区
2. 事务提交时，WAL 缓冲区内容强制刷入磁盘（WAL 日志文件）
3. 实际数据页的修改可能在之后异步写入磁盘（脏页刷盘）
4. 如果系统崩溃，重启时通过重放 WAL 日志恢复到崩溃前的状态

## 在 PostgreSQL 中的应用

- **WAL Writer 进程**：定期将 WAL 缓冲区刷入磁盘
- **Checkpointer 进程**：执行检查点，确保检查点前的 WAL 日志不再需要
- **WAL 段文件**：默认 16MB 一个，循环使用
- **归档模式（Archive Mode）**：将已写满的 WAL 段复制到归档目录，用于 PITR（时间点恢复）

## 为什么 WAL 比直接写数据快？

- 日志是**顺序写入**，数据是**随机写入**（磁盘顺序 I/O 比随机 I/O 快 10-100 倍）
- 日志只记录变更，数据量小
- 数据页可以批量异步刷盘

## 与 MVCC 的关系

[MVCC](MVCC_MVCC.md) 和 WAL 共同构成数据库 ACID 的基石：
- **MVCC** 保证 I（隔离性）和 C（一致性）——通过多版本快照
- **WAL** 保证 D（持久性）和 A（原子性）——通过预写日志和崩溃恢复

## 相关链接

- [PostgreSQL](PostgreSQL_PostgreSQL.md) — PostgreSQL 中的 WAL 实践
- [MVCC](MVCC_MVCC.md) — 多版本并发控制，与 WAL 配合实现 ACID
