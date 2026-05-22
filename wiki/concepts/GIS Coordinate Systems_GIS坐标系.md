---
title: GIS 坐标系与地图投影
type: concept
tags: [gis, coordinate-system, projection]
sources:
 - "[2026-05-21-gis-fundamentals](raw/clippings/2026-05-21-gis-fundamentals.md)"
---

# GIS 坐标系与地图投影

坐标系是 [WebGIS](WebGIS.md) 和所有地理信息系统中最基础的组成部分。

## 地理坐标系 (GCS)
用经纬度来表示地球表面的位置。
- **WGS84 (EPSG:4326)**：国际通用标准，GPS 原生数据、GeoJSON 的默认坐标系。
- **CGCS2000**：中国国家大地坐标系2000。
- **GCJ-02**：火星坐标系，中国国测局加密。
- **BD-09**：百度在 GCJ-02 基础上的加密。

## 投影坐标系 (PCS)
将 3D 地球展平到 2D 平面上，单位通常是米。
- **Web Mercator (EPSG:3857)**：互联网地图屏幕渲染常用。
- **Gauss-Kruger / UTM**：局部区域精确测量常用。

相关概念：[Spatial Data Models_空间数据模型](Spatial%20Data%20Models_空间数据模型.md)，[Spatial Analysis_空间分析](Spatial%20Analysis_空间分析.md)。
