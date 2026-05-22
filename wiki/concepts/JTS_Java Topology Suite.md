---
title: JTS (Java Topology Suite)
type: concept
tags: [gis, java, geometry, topology]
sources:
 - "[2026-05-21-geotools-user-guide](raw/clippings/2026-05-21-geotools-user-guide.md)"
---

# JTS (Java Topology Suite)

JTS（Java Topology Suite）是 Java 生态中最核心的 2D 空间几何与拓扑计算库。它为 [GeoTools](GeoTools.md) 及其他众多 GIS 系统提供了基础的数学模型支撑。

主要功能包括：
- **Geometry**：定义点、线、面等基础几何对象。
- **Geometry Operations**：提供几何操作，如缓冲区（Buffer）、简化（Simplify）、吸附（Snap）、合并等。
- **Geometry Relationships**：基于 DE-9IM 矩阵（九交模型）的空间拓扑关系判断（如相交、包含、重叠等）。

它与 [Spatial Analysis_空间分析](Spatial%20Analysis_空间分析.md) 和 [Spatial Data Models_空间数据模型](Spatial%20Data%20Models_空间数据模型.md) 紧密相关。
