---
title: Java 面试 - 框架与中间件
type: project
tags: [interview, java, spring, redis, mysql, postgresql]
status: in-progress
sources: []
parent: "[[Java 面试复习计划]]"
---

# 框架与中间件

> **[AI 协作指令]** 见：[[Java 面试复习计划#AI 协作指令]]

## Spring / Spring Boot (待完善)

- [ ] IoC 与 AOP
- [ ] Spring Boot 自动配置原理
- [ ] 事务管理

## Redis (待完善)

- [ ] 数据结构与使用场景
- [ ] 缓存穿透/击穿/雪崩
- [ ] 集群方案

## MySQL (待完善)

- [ ] 索引优化
- [ ] 事务隔离级别
- [ ] 主从复制

## PostgreSQL

👉 核心概念总览：[[PostgreSQL_PostgreSQL]]

- [x] **基础与架构**
  - [x] PostgreSQL 相比 MySQL 有哪些核心优势和区别？
    👉 答案见：[[PostgreSQL_PostgreSQL#PostgreSQL vs MySQL]]
  - [x] 解释 PostgreSQL 中的 [[MVCC_MVCC|MVCC]] 实现原理？什么是事务 ID 回卷（Transaction ID Wraparound）？如何预防？
    👉 核心原理见：[[MVCC_MVCC]]
  - [x] 讲讲 PostgreSQL 的进程架构（Postmaster、Backend Process、WAL Writer、Autovacuum 等）？
    👉 答案见：[[PostgreSQL_PostgreSQL#进程架构]]
  - [x] PostgreSQL 有哪些隔离级别？默认是什么？Repeatable Read 下能防止幻读吗？
    👉 答案见：[[PostgreSQL_PostgreSQL#事务隔离级别]]

- [x] **索引与查询优化**
  - [x] PostgreSQL 支持哪些索引类型（B-tree、Hash、GiST、GIN、BRIN）？各自适用什么场景？
    👉 答案见：[[PostgreSQL_PostgreSQL#索引类型详解]]
  - [x] 如何通过 `EXPLAIN ANALYZE` 分析查询性能？关键指标有哪些（Seq Scan、Index Scan、Bitmap Heap Scan、Rows Removed by Filter）？
    👉 答案见：[[PostgreSQL_PostgreSQL#查询优化（EXPLAIN ANALYZE）]]
  - [x] 什么是覆盖索引（Covering Index / INCLUDE 子句）？什么情况下应该使用部分索引（Partial Index）和表达式索引（Expression Index）？
    👉 答案见：[[raw/clippings/2026-05-22-postgresql-deep-dive#覆盖索引与部分索引]]

- [x] **高级特性**
  - [x] JSONB 的原理与优势？如何对 JSONB 建立索引（GIN 索引）？写出复杂 JSONB 查询示例。
    👉 答案见：[[PostgreSQL_PostgreSQL#JSONB]]
  - [x] 什么是表分区？PostgreSQL 支持哪些分区方式（Range、List、Hash）？分区裁剪（Partition Pruning）如何工作？
    👉 答案见：[[PostgreSQL_PostgreSQL#表分区]]
  - [x] 什么是逻辑复制？与物理流复制有什么区别？什么是复制槽（Replication Slot）？CDC 场景下如何管理？
    👉 答案见：[[PostgreSQL_PostgreSQL#逻辑复制 vs 物理流复制]]
  - [x] 什么是 WAL（Write-Ahead Logging）？它的作用和恢复机制是怎样的？
    👉 核心原理见：[[WAL_WAL]]

- [x] **运维与调优**
  - [x] VACUUM 和 Autovacuum 的作用是什么？什么情况下会产生表膨胀（Table Bloat）？如何监控和解决？
    👉 答案见：[[PostgreSQL_PostgreSQL#VACUUM 与表膨胀]]
  - [x] PostgreSQL 中常见的锁类型有哪些？如何排查锁等待和死锁问题？
    👉 答案见：[[PostgreSQL_PostgreSQL#锁排查]]
  - [x] 什么是连接池？PgBouncer 的事务级与会话级连接池有什么区别？
    👉 答案见：[[PostgreSQL_PostgreSQL#PgBouncer 连接池]]
  - [x] 如何设计 PostgreSQL 的多租户架构？行级安全（Row-Level Security, RLS）如何实现？
    👉 答案见：[[PostgreSQL_PostgreSQL#多租户与 RLS]]
