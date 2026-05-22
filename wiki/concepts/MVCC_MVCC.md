---
title: MVCC（多版本并发控制）
type: concept
tags: [database, concurrency, transaction, postgresql, mysql]
sources:
  - "[2026-05-22-postgresql-interview-questions](raw/clippings/2026-05-22-postgresql-interview-questions.md)"
  - "[2026-05-22-postgresql-deep-dive](raw/clippings/2026-05-22-postgresql-deep-dive.md)"
---

# MVCC（Multi-Version Concurrency Control）

MVCC（多版本并发控制）是数据库管理系统实现高并发读写的核心机制，允许**读不阻塞写、写不阻塞读**。

## 核心原理

- 每行记录包含隐藏的系统字段，标记其版本归属
- 更新操作不就地覆盖，而是生成新版本（元组），旧版本保留
- 每个事务启动时获取一个快照（Snapshot），确定可见的事务集合
- 读操作读取快照可见的合适版本，不受并发写影响

## PostgreSQL 的实现

- `xmin`：创建该行版本的事务 ID
- `xmax`：删除/更新该行版本的事务 ID
- `t_ctid`：指向同一行的下一个更新版本（形成版本链）
- 事务状态存储在 `pg_xact`（提交日志）中，用于快速判断事务是否已提交
- 死亡元组（dead tuples）由 **VACUUM** 清理回收空间

### 事务 ID 回卷（XID Wraparound）

- PostgreSQL 使用 32 位事务 ID（约 42 亿个）
- 分配约 20 亿个后，新事务 ID 从 3 重新开始
- 旧事务 ID（如 5）变成"未来事务"，导致可见性判断错误
- **预防**：Autovacuum 自动冻结旧元组为 `FROZEN` 状态；监控 `datfrozenxid`；配置 `autovacuum_freeze_max_age`

## MySQL（InnoDB）的实现

- 旧版本存储在独立的 **undo log** 中
- read view 判断事务可见性
- 回滚段管理和清理由 purge 系统异步处理

## 两种实现的对比

| 维度 | PostgreSQL | MySQL InnoDB |
|------|-----------|-------------|
| 旧版本存储 | 堆表中的死亡元组 | 独立的 undo log |
| 空间回收 | VACUUM 清理 | purge 系统异步清理 |
| 清理开销 | 可能产生表膨胀 | undo log 可能撑爆 |
| 可见性判断 | xmin/xmax + 事务状态 | read view + 回滚段 |

## 相关链接

- [PostgreSQL](PostgreSQL_PostgreSQL.md) — PostgreSQL 中的 MVCC 实践
- [](Java%20面试复习计划#四、框架与中间件%20(待完善)#四、框架与中间件%20(待完善)#PostgreSQL) — 面试考点
- [WAL 预写日志](WAL_WAL.md) — 与 MVCC 共同保证事务持久性
