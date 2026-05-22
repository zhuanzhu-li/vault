---
title: WebGIS
type: concept
tags: [gis, webgis, architecture]
sources:
 - "[2026-05-21-gis-fundamentals](raw/clippings/2026-05-21-gis-fundamentals.md)"
---

# WebGIS

在 Web 端展示和处理地图的技术体系。

## OGC 服务标准
基于 [OGC_开放地理空间信息联盟](OGC_开放地理空间信息联盟.md) 的标准：
- **WMS (Web Map Service)**：渲染图片发给前端。
- **WFS (Web Feature Service)**：发送矢量数据给前端。
- **WMTS (Web Map Tile Service)**：切片（瓦片）服务。
- **MVT (Mapbox Vector Tile)**：矢量瓦片，现代主流。

## 主流 GIS 技术栈
- **空间数据库**：PostgreSQL + PostGIS，MySQL (Spatial)。
- **服务端发布**：GeoServer，Tippecanoe。
- **前端渲染引擎**：Leaflet，OpenLayers，Mapbox GL JS，Cesium。
- **桌面端**：QGIS，ArcGIS。
- **底层库**：GDAL/OGR，Turf.js，以及 Java 生态的主流底层库 [GeoTools](GeoTools.md)。
