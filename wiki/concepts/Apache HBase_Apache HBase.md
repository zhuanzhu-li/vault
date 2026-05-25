---
title: Apache HBase
type: concept
tags: [database, nosql, big-data, distributed-systems]
aliases:
  - HBase
  - Apache HBase
sources:
  - "[2026-05-25-hbase-reference-guide-preface](raw/clippings/2026-05-25-hbase-reference-guide-preface.md)"
---

# Apache HBase

Apache HBase 是一个运行于 HDFS 之上的分布式、可扩展的 NoSQL 大数据存储系统。它为海量结构化数据提供实时的随机读写访问能力，是 Google Bigtable 的开源实现。

## 核心特性

- **线性与模块化扩展**：通过添加 RegionServer 节点实现水平扩展
- **强一致性**：单行读写保证强一致性
- **自动分片**：表自动按 Region 横向切分，随数据增长自动分裂
- **HDFS 集成**：底层存储依托 Hadoop 分布式文件系统，数据自动冗余备份
- **MapReduce 集成**：可直接作为 MapReduce 作业的数据源和输出目标
- **Java API 生态**：提供原生 Java 客户端，广泛集成于大数据生态

## 文档体系

HBase 官方参考指南使用 [Fumadocs](Fumadocs_Fumadocs.md) + MDX 构建，源代码位于 HBase 仓库的 `hbase-website/app/pages/_docs/docs/_mdx/(multi-page)` 目录。通过 `mvn site` 命令生成。

## 运行在分布式系统之上

HBase 运行于分布式环境，其运行可能因多种原因出现波动——从 HBase 自身的 Bug，到操作系统或硬件层面的问题（如网卡驱动程序 Bug 或内存总线带宽不足）。这体现了[分布式计算](Distributed%20Computing_分布式计算.md)的固有挑战。

## 相关

- [分布式计算](Distributed%20Computing_分布式计算.md)
- [Apache 软件基金会](Apache%20Software%20Foundation_Apache软件基金会.md)
- [Fumadocs](Fumadocs_Fumadocs.md)
