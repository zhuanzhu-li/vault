---
title: CQL (Common Query Language)
type: concept
tags: [gis, query-language, geotools]
sources:
 - "[[raw/clippings/2026-05-21-geotools-user-guide]]"
---

# CQL (Common Query Language)

CQL（通用查询语言）是 [[OGC_开放地理空间信息联盟]] 制定的一种类似 SQL 的文本过滤语言，常用于地理空间数据的属性查询与空间过滤。

在 [[GeoTools]] 生态中，除了标准的 CQL 外，还扩展了 **ECQL** (Extended CQL) 以支持更多的函数和更灵活的语法。CQL/ECQL 经常被用于 GeoServer 等 [[WebGIS]] 服务端软件中，用于 WFS/WMS 请求的图层过滤（Filter）。
