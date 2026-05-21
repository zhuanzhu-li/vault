---
title: 空间数据模型
type: concept
tags: [gis, spatial-data]
sources:
 - "[[raw/clippings/2026-05-21-gis-fundamentals]]"
---

# 空间数据模型

计算机如何存储现实世界中的山川、河流和建筑？主要分两种基本数据结构。

## 矢量数据 (Vector)
用数学坐标来描述几何图形。分为点 (Point)、线 (LineString)、面 (Polygon)。
- **特点**：缩放不失真，文件体积小，适合表达边界明确的实体。
- **常见格式**：Shapefile, GeoJSON, KML/KMZ, WKT/WKB。

## 栅格数据 (Raster)
由像素网格组成，每个像素有一个值。
- **特点**：适合表达连续变化的地理现象，如卫星影像、高程数据 (DEM)。
- **常见格式**：GeoTIFF。

相关概念：[[GIS Coordinate Systems_GIS坐标系]]，[[Spatial Analysis_空间分析]]。
