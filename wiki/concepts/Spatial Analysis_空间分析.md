---
title: 空间分析
type: concept
tags: [gis, spatial-analysis]
sources:
 - "[2026-05-21-gis-fundamentals](raw/clippings/2026-05-21-gis-fundamentals.md)"
---

# 空间分析

空间分析是计算空间关系的核心。

## 主要分析类型
- **拓扑/空间关系**：判断几何图形的关系（相交、包含、相邻等）。
- **缓冲区分析 (Buffer)**：以特定几何体为中心向外扩展的多边形。
- **叠加分析 (Overlay)**：多数据层的布尔运算（交集、并集等）。
- **网络分析 (Network Analysis)**：基于图论的路径规划（如 Dijkstra）。
- **空间插值 (Spatial Interpolation)**：预测未知区域的值（如 Kriging）。

依赖的数据结构见 [Spatial Data Models_空间数据模型](Spatial%20Data%20Models_空间数据模型.md)。
