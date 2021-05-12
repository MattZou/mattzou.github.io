---
layout: post
title: 路网数据获取
date: 2018-05-06
updated: 
author: MattZou
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/CycleLayer2.jpg/bg
banner_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/Spatial-distribution-of-Chinas-OSM-road-network.png/bg
categories: 软件使用
tags:
- 路网
- OSM
- Mapbox
- ArcGIS
description: 路网数据获取与处理方式总结
---

在道路移动源研究中，路网数据获取与处理是重要的基础工作，路网的准确与详细程度决定了最终研究成果的空间分辨率，经过这么几年踩坑后，总结一下路网数据获取相关经验。

研究中使用的路网数据可以简单分为在线和离线两类，前者多用于展示背景图，后者用于数据分析。
> 注意：一切地理数据首要考虑的是合规性，包括且不限于获取方式，公开方式，内容标注等。

## 在线
在线地图国内外都有很多提供商，主要区别在于坐标体系以及地区标注规范。
### 国内
国内图商包括互联网公司如[百度](http://lbsyun.baidu.com/)、[高德](https://lbs.amap.com/)、[四维图新](https://nglp.autoai.com/docs/websdk/summary.html)以及国家标准地图服务[天地图](https://www.tianditu.gov.cn/)。

受国内测绘保密要求以及各家公司产品排他性等因素影响，这些地图都有各自投影坐标体系，对于WGS84体系下采集的数据需要通过各自平台提供的开发接口进行坐标转换，转换后才能避免坐标偏移的问题。各类坐标转换可以参考[^1]，并按照各家最新API文档进行操作。

### 国外
国外在线地图推荐使用[Mapbox](https://studio.mapbox.com/)，Mapbox提供了多种预设[Style](https://www.mapbox.com/gallery/)，在Studio中定义显示样式，发布地图后可以在各端使用。Mapbox地图要素主要来源于OSM，坐标体系为WGS84，更方便使用。

但默认`Mapbox`地图存在地区标记错误，比如台湾省等，对此问题推荐使用以下通过中国政府审核的地图styles。
```
Streets
mapbox://styles/mapbox/streets-zh-v1

Dark
mapbox://styles/mapbox/dark-zh-v1

Light
mapbox://styles/mapbox/light-zh-v1
```
在线地图多数情况下无法供地理要素提取，因此难以独立操作路网数据，多数情况还是作为可视化时的底图使用。

## 离线
离线地图主要以shp、GeoJson、CAD等形式存储提供，在研究分析中以这类数据为主。

### 测绘地图
测绘类地图更多是在各类项目实施中使用，这类路网地图特点是要素齐全，绘图标准，各类拓扑冲突少，格式以CAD、shp为主。但此类地图更新往往较慢，并且有较高保密要求，因此多数在项目实施主体内部流动。

### OSM获取
#### 下载
OpenStreetMap作为目前为止最为成功的开放地图计划，是目前主流地图要素的获取源。可以通过OSM获取研究区域所需路网要素，OSM网页提供在线区域选定及导出服务，但选定范围有限(nodes limit is 50000)，要实现大范围下载较为复杂，因此推荐通过以下几种方式实现。
1. Geofabrik[下载](http://download.geofabrik.de/asia/china.html)下载shp。由于内容过大，电脑配置不够谨慎使用。
   
2. pbf全图提取
   - Geofabrik[下载](http://download.geofabrik.de/asia/china.html)中国区`pbf`内容
   - [在此](https://www.openstreetmap.org/export#map=9/36.3914/120.3223)通过`手动选择不同的区域`找到提取区域坐标范围
   - 通过Osmosis提取特定范围内要素，生成osm文件
     ``` bat
     C:\Program Files (x86)\osmosis-latest\bin>osmosis --read-xml file= china-latest.osm 
     --bounding-box left="119.3692" right="120.9265" top="37.1362" bottom="35.5903" --write-xml file=qd.osm  
     ``` 
   - ArcGIS中通过对应版本[`Arcgis Editor For OSM`](https://www.esri.com/en-us/arcgis/products/arcgis-editor-for-openstreetmap)工具箱打开并转换为shp
  
3. Overpass API
   - [在此](http://www.overpass-api.de/query_form.html)的`Overpass API Query Form`填入查询代码，点击Query保存Interpreter文件
    ``` xml
    <osm-script>
        <query type="relation">
            <has-kv k="boundary" v="administrative"/>
            <has-kv k="name:zh" v="天津市"/>
        </query>
    <print/></osm-script>
    ```
    - 打开Interpreter文件，将其中`<area-query ref=""/>`的ref值加上360000000，将以下内容填入`Overpass API Query Form`，再次Query，保存文件就是路网osm文件
    ``` xml
    <osm-script timeout="1800" element-limit="100000000">
    <union>
        <area-query ref="原始值加360000000"/>
        <recurse type="node-relation" into="rels"/>
        <recurse type="node-way"/>
        <recurse type="way-relation"/>
    </union>
    <union>
        <item/>
        <recurse type="way-node"/>
    </union>
    <print mode="body"/>
    </osm-script>
    ```
    - ArcGIS中通过对应版本[`Arcgis Editor For OSM`](https://www.esri.com/en-us/arcgis/products/arcgis-editor-for-openstreetmap)工具箱打开并转换为shp[^2]

#### 道路类型
OSM路网下载后，属性表字段fclass可以用于区分道路类型[^3]。
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/OSMType.webp)

#### 车速对应
道路类型与车速对应关系[^4]。
```
"highway" 字段，中英对照及速度（km/h）
   'bridleway' 马道  10
   'construction' 建设中 0
   'cycleway' 自行车道 15
   'footway' 步行 5
   'living_street' 街区 5
   'motorway' 高速公路  50
   'motorway_link' 高速公路连接处  50 
   'path' 路 5
   'pedestrian' 人行道 5
   'platform' 月台 5
   'primary' 主干道 40
   'primary_link'  主干道连接处 40 
   'raceway' 赛道 30
   'residential'  居住区道路 5
   'road' 所有不知名的道路 10
   'secondary'  次干道 30
   'secondary_link'  次干道连接处 30
   'service' 通往设施的道路 10
   'steps'  阶梯 5 
   'tertiary' 三级道路 10
   'tertiary_link' 三级道路连接处 10
   'track' 轨道 5
   'trunk' 支路 50
   'trunk_link' 支路链接处 50
   'unclassified' 未分类道路 20
"railway" 字段，中英对照及速度（km/h）
   'subway' 地铁 50
   'rail'  火车  40
```
#### 其他
OSM还通过子项目形式完善特殊道路类型数据
- [中国铁路项目](https://wiki.openstreetmap.org/wiki/China_Railways)
- [中国国道项目](https://wiki.openstreetmap.org/wiki/China_National_Highway)
- [中国高速项目](https://wiki.openstreetmap.org/wiki/Expressways_of_China)

### GRIP获取
除了细粒度路网，还可以[GRIP](https://www.globio.info/download-grip-dataset)项目获取路网数据。
> The Global Roads Inventory Project (GRIP) dataset was developed to provide a more recent and consistent global roads dataset for use in global environmental and biodiversity assessment models like GLOBIO.

以网格的形式提供道路清单，可下载矢量shp格式或raster格式，对于大尺度研究，这个项目提供了较好的数据支撑。




## Reference
[^1]: [国内地图坐标转换](https://mattzou.com/2019/03/25/Python-%E5%9D%90%E6%A0%87%E8%BD%AC%E6%8D%A2/)
[^2]: [从Openstreetmap获取路网数据并制作shapefile图层](https://zhuanlan.zhihu.com/p/141740446)
[^3]: [ArcGIS路网分析之OSM路网数据获取](https://www.bilibili.com/read/cv9256335/)
[^4]: [openstreetmap道路网数据对应中国道路速度——武汉](https://blog.csdn.net/u011994016/article/details/56831190)