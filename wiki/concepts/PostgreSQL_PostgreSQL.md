---
title: PostgreSQL
type: concept
tags: [database, sql, relational-database, postgresql]
sources:
  - "[2026-05-22-postgresql-interview-questions](raw/clippings/2026-05-22-postgresql-interview-questions.md)"
  - "[2026-05-22-postgresql-deep-dive](raw/clippings/2026-05-22-postgresql-deep-dive.md)"
---

# PostgreSQL

PostgreSQL（常称 Postgres）是一种开源的对象关系型数据库管理系统，以其 ACID 事务、[MVCC](MVCC_MVCC.md) 并发控制、可扩展性和对标准 SQL 的高度兼容而闻名。

## 核心特性

- **[MVCC](MVCC_MVCC.md)**：多版本并发控制，读不阻塞写，写不阻塞读
- **ACID 事务**：完全支持原子性、一致性、隔离性、持久性
- **丰富的索引类型**：B-tree、Hash、GiST、GIN、BRIN、SP-GiST
- **扩展生态**：[PostGIS](PostGIS)、PGVector、pg_cron、Citus 等
- **JSONB 支持**：原生二进制 JSON 存储，支持 GIN 索引
- **逻辑复制与流复制**：支持物理流复制和逻辑复制
- **[WAL 预写日志](WAL_WAL.md)**：保证事务持久性和崩溃恢复

## PostgreSQL vs MySQL

| 维度 | PostgreSQL | MySQL |
|------|-----------|-------|
| **ACID 严格性** | 完全 ACID，Serializable 真正可串行化 | InnoDB 支持 ACID |
| **MVCC** | 旧版本在堆表，xmin/xmax | 旧版本在 undo log |
| **索引类型** | B-tree、Hash、GiST、GIN、BRIN | 主要是 B-tree |
| **扩展性** | Extension 机制强大 | 插件生态较少 |
| **JSON** | JSONB 原生二进制，GIN 索引 | 基础 JSON |
| **并发模型** | 多进程架构 | 多线程架构 |
| **许可证** | PostgreSQL 许可（类似 MIT） | 双许可 |

## 进程架构

PostgreSQL 采用**多进程架构**：

- **Postmaster（主进程）**：守护进程，监听端口，fork 出其他进程
- **Backend Process**：每个客户端连接对应一个独立进程
- **WAL Writer**：将 [WAL](WAL_WAL.md) 缓冲区刷入磁盘
- **Autovacuum Launcher / Worker**：自动清理死亡元组
- **Checkpointer**：执行检查点，脏页刷盘
- **BgWriter**：定期将脏页写入磁盘

## 事务隔离级别

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|---------|:---:|:---------:|:---:|
| Read Uncommitted | 可能 | 可能 | 可能 |
| Read Committed（默认） | 不可能 | 可能 | 可能 |
| Repeatable Read | 不可能 | 不可能 | **不可能** |
| Serializable | 不可能 | 不可能 | 不可能 |

PostgreSQL 的 Repeatable Read 通过**快照隔离**完全防止幻读（与 MySQL 依赖 Gap Lock 不同）。

## 索引类型详解

| 类型 | 适用场景 | 特点 |
|------|---------|------|
| **B-tree**（默认） | 等值/范围查询、排序 | 最通用 |
| **Hash** | 等值查询 | 仅 `=`，不支持排序 |
| **GiST** | 地理空间、全文搜索 | 自定义操作符，用于 PostGIS |
| **GIN** | JSONB、数组、全文搜索 | 倒排索引，适合多值类型 |
| **BRIN** | 超大表、顺序插入 | 体积极小，维护成本低 |

## 查询优化（EXPLAIN ANALYZE）

关键指标解读：
- **Seq Scan**：全表扫描，暗示缺索引
- **Index Scan**：B-tree 索引扫描
- **Index Only Scan**：覆盖索引，不回表
- **Bitmap Heap Scan**：通过位图按物理顺序读堆
- **Rows Removed by Filter**：值过大说明索引不当
- **Plan Rows vs Actual Time**：偏差大需 **ANALYZE** 更新统计

## 高级特性

### JSONB
- 二进制存储，去重键、排序键
- 支持 `@>`（包含）、`?`（键存在）、`->>`（取值）等操作符
- 配合 GIN 索引高效查询半结构化数据

### 表分区
- **Range 分区**：日期范围
- **List 分区**：枚举值
- **Hash 分区**：哈希分布
- 分区裁剪（Partition Pruning）自动跳过无关分区

### 逻辑复制 vs 物理流复制
| 维度 | 物理流复制 | 逻辑复制 |
|------|-----------|---------|
| 级别 | 实例级 | 表级 |
| 版本 | 必须相同 | 可跨版本 |
| 场景 | 高可用、读写分离 | CDC、数据迁移 |

复制槽需监控，防止 WAL 堆积。

## 运维与调优

### VACUUM 与表膨胀
- VACUUM 回收死亡元组、更新可见性映射、冻结旧事务 ID
- 表膨胀原因：Autovacuum 配置不当、长事务、大量 UPDATE/DELETE
- 监控：`pg_stat_user_tables.n_dead_tup`

### 锁排查
```sql
SELECT blocked_locks.pid AS blocked_pid,
       blocking_locks.pid AS blocking_pid,
       blocked_activity.query AS blocked_query
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_locks.pid = blocked_activity.pid
JOIN pg_catalog.pg_locks blocking_locks ...
WHERE NOT blocked_locks.granted;
```

### PgBouncer 连接池
| 模式 | 说明 | 适用 |
|------|------|------|
| Session 级 | 客户端断连才释放 | 会话级状态 |
| Transaction 级（推荐） | 事务结束即回收 | OLTP 场景 |
| Statement 级 | 每条语句后回收 | 无事务场景 |

### 多租户与 RLS
```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_isolation ON orders
    USING (tenant_id = current_setting('app.tenant_id')::INT);
```

## 面试高频考点

详见 [](Java%20面试复习计划#四、框架与中间件%20(待完善)#四、框架与中间件%20(待完善)#PostgreSQL)

## 相关链接

- [MVCC](MVCC_MVCC.md) — 多版本并发控制详解
- [WAL 预写日志](WAL_WAL.md) — 事务持久性机制
- [Vector Database](Vector%20Database.md) — PGVector 与向量数据库
