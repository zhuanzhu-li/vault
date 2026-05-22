---
title: GeoTools Architecture
type: concept
tags: [gis, geotools, architecture, java]
sources:
 - "[2026-05-21-geotools-user-guide](raw/clippings/2026-05-21-geotools-user-guide.md)"
---

# GeoTools 架构 (GeoTools Architecture)

[GeoTools](GeoTools.md) 是一个开源的 Java GIS 工具包。其架构包含多个核心模块与扩展：
- **Data API / DataStore**：提供统一的数据访问接口，支持 JDBC、Shapefile、GeoPackage 等格式。
- **Coverage**：处理栅格数据（Raster），如 GeoTIFF、Image 插件等。
- **Render**：提供地图渲染能力（GTRenderer），支持解析 SLD 样式文件并绘制地图。
- **XML**：处理各类地理空间 XML 标准（如 GML、KML 等）。
- **Extensions**：提供如图计算（Graph）、应用模式支持（App-schema）、矢量网格（Vector grids）等进阶功能。

GeoTools 底层强依赖于 [JTS_Java Topology Suite](JTS_Java%20Topology%20Suite.md) 进行几何对象模型与拓扑关系计算。此外，它支持 [CQL_Common Query Language](CQL_Common%20Query%20Language.md) 进行空间数据的过滤与查询。
