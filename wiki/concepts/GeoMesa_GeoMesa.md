---
title: GeoMesa
type: concept
tags: [gis, big-data, spatial-database, hbase, locationtech]
sources:
  - "[2026-05-26-geomesa-quickstart-hbase](raw/clippings/2026-05-26-geomesa-quickstart-hbase.md)"
---

# GeoMesa

GeoMesa 是一个开源的时空大数据引擎，为 Apache HBase、Apache Cassandra、Apache Kafka、Redis 等分布式数据库提供时空索引和查询能力。它使海量时空数据的实时存储和分析成为可能。

## 核心特性

| 特性 | 说明 |
|------|------|
| **多存储后端** | 支持 HBase、Cassandra、Kafka、Redis、Accumulo 等 |
| **时空索引** | 基于 Z-Order 曲线或 XZ-Order 曲线的高效时空索引 |
| **标准查询语言** | 支持 CQL/ECQL 过滤，与 GeoTools 无缝集成 |
| **GeoServer 集成** | 可作为 GeoServer 的数据源进行可视化发布 |
| **分布式处理** | 通过 Spark 进行大规模时空分析 |

## 架构组件

### 分布式运行时

GeoMesa 需要在 HBase/Cassandra 集群中安装分布式运行时 JAR 包。运行时包含：

- **协处理器 (Coprocessors)**：在数据节点执行时空索引和查询过滤
- **索引格式**：将时空数据编码为可查询的键值对

### 数据模型

GeoMesa 使用 GeoTools 的 SimpleFeatureType 定义数据 Schema，支持：

- 空间几何类型：Point、LineString、Polygon 等
- 时间属性：Date 类型字段
- 普通属性：String、Integer 等

### 索引策略

| 策略 | 适用场景 |
|------|---------|
| **Z-Order** | 时空范围查询、最近邻查询 |
| **XZ-Order** | 面状区域覆盖查询 |
| **Hilbert** | 密集点数据的空间排序 |

## 使用场景

| 场景 | 示例 |
|------|------|
| **轨迹分析** | 车辆/船舶/航班轨迹存储与查询 |
| **物联网时序** | 传感器数据 + 位置信息的时空聚合 |
| **地理围栏** | 实时判断目标是否进入指定区域 |
| **海量 POI** | 亿级地理标记数据的快速检索 |

## 快速入门流程

1. 在 HBase 集群中安装 GeoMesa 分布式运行时
2. 使用 Maven 构建 GeoMesa 项目
3. 定义 SimpleFeatureType Schema
4. 创建 DataStore 并写入数据
5. 使用 CQL 查询时空数据
6. 通过 GeoServer 或 Leaflet 可视化

## 相关

- [Apache HBase_Apache HBase](Apache%20HBase_Apache%20HBase.md) — GeoMesa 的底层存储之一
- [GeoTools_GeoTools](GeoTools.md) — GeoMesa 依赖的空间数据处理库
- [CQL_Common Query Language](CQL_Common%20Query%20Language.md) — GeoMesa 查询接口
- [Spatial Data Models_空间数据模型](Spatial%20Data%20Models_空间数据模型.md) — 时空数据模型基础
