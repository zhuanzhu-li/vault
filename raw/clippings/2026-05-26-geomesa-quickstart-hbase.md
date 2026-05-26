---
title: "GeoMesa HBase Quick Start"
source-url: https://www.geomesa.org/documentation/stable/tutorials/geomesa-quickstart-hbase.html
captured-date: 2026-05-26
author: GeoMesa Community
---

# GeoMesa HBase Quick Start

本教程是使用 GeoMesa + HBase 最快速、最简单的入门指南。是通往其他更复杂教程的良好起点。

## 教程内容

本教程仅做几件小事：

1. 建立一个新的静态 SimpleFeatureType
2. 准备 HBase 表来存储此类型数据
3. 创建数千个示例 SimpleFeatures
4. 将这些 SimpleFeatures 写入 HBase
5. 按给定地理矩形、时间范围和属性过滤器进行查询
6. 使用 GeoServer 可视化数据（可选）

## 前置条件

- Java JDK 11 或 17
- Apache Maven 3.6.3+
- Git 客户端
- HBase 2.6 实例
- GeoMesa HBase 分布式运行时

## 核心概念

### SimpleFeatureType

定义空间要素的数据结构。本教程使用 GDELT 数据集，Schema 包含：

- GLOBALEVENTID: String
- Actor1Name: String
- Actor1CountryCode: String
- Actor2Name: String
- ActionGeo_Type: Integer
- ActionGeo_FullName: String
- ActionGeo_CountryCode: String
- dtg: Date
- geom: Point:srid=4326

### 分布式运行时

GeoMesa 需要在 HBase 集群中安装分布式运行时 JAR 包，并注册协处理器（Coprocessors）来实现时空索引功能。

### CQL 查询

使用 GeoTools 的上下文查询语言（CQL）进行时空和属性过滤：

- 空间查询：`BBOX(geom, -120.0, 30.0, -75.0, 55.0)`
- 时间查询：`dtg DURING 2017-12-31T00:00:00+00:00/2018-01-02T00:00:00+00:00`
- 属性查询：`EventCode = '051'`

## GDELT 数据集

GDELT（全球事件、语言、情感数据）是一个每 15 分钟更新一次的大型开放数据项目。本教程使用其中包含经纬度坐标和事件代码的子集。

## 可视化选项

1. **Leaflet**：使用 GeoMesa HBase tools 的 export 命令直接输出 Leaflet 地图
2. **GeoServer**：更生产级的方案，连接 GeoServer 进行可视化，支持热力图和样式配置

## 百科链接

- [GeoMesa_GeoMesa](wiki/concepts/GeoMesa_GeoMesa.md)
- [Apache HBase_Apache HBase](wiki/concepts/Apache%20HBase_Apache%20HBase.md)
- [GeoTools_GeoTools](wiki/entities/GeoTools.md)
- [CQL_Common Query Language](wiki/concepts/CQL_Common%20Query%20Language.md)
