---
title: GIS 基础知识体系梳理
captured-date: 2026-05-21
source-url: Conversation
author: Agent & User
tags:
  - gis
  - webgis
---

地理信息系统（**GIS**, Geographic Information System）是一个非常庞大且跨学科的领域，结合了地理学、几何学、计算机科学和数据分析。

我们可以将 GIS 的基础知识点拆解为以下几个核心模块来梳理：

### 1. 坐标系与地图投影（Coordinate System & Projection）
这是 GIS 中最基础、也是初学者最容易踩坑的地方。地球是一个不规则的椭球体，如何将它上面的位置用数字表示，并画在平面的屏幕上？
*   **地理坐标系（GCS, Geographic Coordinate System）**：
    *   **概念**：用**经纬度**（球面坐标）来表示地球表面的位置。
    *   **常见标准**：
        *   **WGS84**（EPSG:4326）：国际通用标准，GPS 原生数据、GeoJSON 的默认坐标系。
        *   **CGCS2000**：中国国家大地坐标系2000，国内政府和官方项目常用，与 WGS84 误差极小。
        *   **GCJ-02**（火星坐标系）：中国国测局制定的加密坐标，国内的高德、腾讯地图底图强制使用。
        *   **BD-09**：百度在 GCJ-02 基础上再次加密的坐标系。
*   **投影坐标系（PCS, Projected Coordinate System）**：
    *   **概念**：将 3D 的地球表面“剥下来”展平到 2D 平面上，单位通常是**米（m）**。投影必然会带来变形（面积、角度或距离的变形）。
    *   **常见标准**：
        *   **Web Mercator**（EPSG:3857）：几乎所有互联网地图（Google Maps, 高德, Mapbox）在屏幕渲染时使用的投影。特点是角度不变形，但高纬度地区面积会被极度放大（比如格陵兰岛看起来和非洲一样大）。
        *   **高斯-克吕格（Gauss-Kruger）** / **UTM**：常用于局部区域的精确测量和工程制图。

### 2. 空间数据模型（Spatial Data Models）
计算机如何存储现实世界中的山川、河流和建筑？主要分两种基本数据结构：
*   **矢量数据（Vector）**：
    *   用数学坐标来描述几何图形。分为三种基本类型：**点（Point）**、**线（LineString）**、**面（Polygon）**。
    *   **优点**：缩放不失真，文件体积小，适合表达边界明确的实体（如道路、行政区划、POI 点）。
    *   **常见格式**：`Shapefile (.shp)`（传统行业标准）、`GeoJSON`（Web 端最常用）、`KML/KMZ`（Google Earth 常用）、`WKT/WKB`（数据库存储格式）。
*   **栅格数据（Raster）**：
    *   由像素网格（Grid）组成，每个像素有一个值。
    *   **优点**：适合表达连续变化的地理现象，如卫星影像（RGB 值）、高程数据DEM（海拔高度）、温度图。
    *   **常见格式**：`GeoTIFF`（带坐标信息的 TIFF 图片）。

### 3. 空间关系与空间分析（Spatial Analysis）
GIS 不仅仅是“在地图上画点”，其核心在于计算空间关系：
*   **拓扑/空间关系**：判断两个几何图形的关系。例如：相交（Intersect）、包含（Contains）、相邻（Touches）、不相交（Disjoint）。
*   **缓冲区分析（Buffer）**：以某个点、线、面为中心，向外扩展一定距离形成的多边形。（如：寻找距离地铁站 500 米范围内的学区房）。
*   **叠加分析（Overlay）**：将多个数据层叠在一起进行布尔运算（如：求交集 Clip、并集 Union）。
*   **网络分析（Network Analysis）**：基于图论，常用于路径规划（最短路径 Dijkstra/A*）、服务区分析。
*   **空间插值（Spatial Interpolation）**：根据已知的离散点数据，预测未知区域的值（如：根据几个气象站的数据，推算出全省的温度分布图，常用算法如 Kriging、IDW）。

### 4. WebGIS 与 OGC 服务标准
如果要在 Web 端展示地图，不能把几 GB 的地图数据直接发给前端，需要通过服务端发布标准的接口（通常遵循 **OGC** 开放地理空间信息联盟标准）：
*   **WMS（Web Map Service）**：服务端把数据渲染成普通的**图片（PNG/JPG）**发给前端。前端只负责显示图片，无法修改样式。
*   **WFS（Web Feature Service）**：服务端把**矢量数据（如 GeoJSON）**发给前端。前端可以拿到具体坐标并用代码控制渲染样式。
*   **WMTS（Web Map Tile Service）**：**切片（瓦片）服务**。把全球地图提前切成一张张 $256 \times 256$ 像素的小正方形图片。前端根据当前的缩放级别（Zoom）和屏幕视口，按需请求对应的小正方形拼接成地图。
*   **MVT（Mapbox Vector Tile）**：**矢量瓦片**。现代 WebGIS 的主流，将矢量数据切成小块发给前端，前端利用 WebGL 进行高性能渲染，既能按需加载，又能动态改变地图样式。

### 5. 主流 GIS 技术栈
构建一个 GIS 系统的常见工具链：
*   **空间数据库**：**PostgreSQL + PostGIS**（绝对的行业标准，提供了丰富的空间计算函数）、MySQL (Spatial)、Redis (Geo)。
*   **服务端发布/中间件**：**GeoServer**（Java 生态，最常用）、MapServer（C/C++ 生态）、Tippecanoe（制作矢量瓦片神器）。
*   **前端渲染引擎**：
    *   **Leaflet**：轻量级，简单易用，适合传统的 2D 业务需求。
    *   **OpenLayers**：老牌、功能重度且全面，常用于传统 WebGIS 项目。
    *   **Mapbox GL JS** / **MapLibre GL JS**：基于 WebGL，支持矢量瓦片，性能极高，适合现代化、酷炫的 2D/2.5D 交互地图。
    *   **Cesium**：基于 WebGL 的 3D 地球引擎，数字孪生、智慧城市、BIM/CIM 领域的绝对霸主。
*   **桌面端处理软件**：**QGIS**（开源免费，极其强大，工程师最爱）、**ArcGIS**（商业收费，传统 GIS 行业的巨头）。
*   **底层核心库**：GDAL/OGR（用于数据格式转换的 C++ 底层库，各大软件都在用它）、GEOS（几何拓扑计算库）、Turf.js（前端轻量级空间分析算法库）。

## 百科链接

- [GIS 坐标系](GIS%20Coordinate%20Systems_GIS坐标系.md)
- [空间数据模型](Spatial%20Data%20Models_空间数据模型.md)
- [空间分析](Spatial%20Analysis_空间分析.md)
- [WebGIS](WebGIS.md)
- [OGC](OGC_开放地理空间信息联盟.md)