---
layout: post
title: ArcGIS 载入在线底图
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/Addbasemap.jpeg/bg
banner_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/Mapbox.jpeg/bg
author: MattZou
date: 2018/07/09
updated: 
categories:
- ArcGIS
tags: 
- ArcGIS
- BaseMap
description: ArcGIS载入在线底图方法介绍
---

ArcGIS专题图绘制需载入在线底图，除自带ArcGIS Online的底图外，本文介绍另外两种底图载入方式。
## ArcBruTile
ArcBruTile是一个独立的底图插件，支持主流在线地图插入，0.8以后版本收费，并支持ArcGIS Pro，可按需[使用](https://bertt.itch.io/arcbrutile)

## Mapbox
ArcGIS可以连接 WMTS 服务，因此可以通过Mapbox Studio自定义地图，并发布在线服务，提供给ArcGIS使用。主要步骤如下：
1. [Studio](https://studio.mapbox.com/) 中基于默认地图新建并修改样式
2. 发布修改后的地图为 WMTS 服务
   ![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/MapboxShare.jpg/pic)
3. 在ArcGIS中，选择添加WMTS服务
   ![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/ArcGISWMTS.jpg/pic)
   将发布的url服务地址填入，用户名密码留空
   ![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/WMTS.jpg/pic)
4. 在ArcGIS中插入底图
   将服务中的图层拖入Layer Frame即可
   ![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/MapboxWMTS.jpg/pic)

推荐使用Mapbox底图
- Mapbox地图坐标系统为WGS84，方便转换与匹配
- 样式可以随意更改，底图自动同步
