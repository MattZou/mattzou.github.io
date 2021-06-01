---
title: 加油站数据获取
layout: post
author: MattZou
updated: 2021-05-31
date: 2021-05-31
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/fuelstation.webp/bg
categories: 交通
tags:
- 高德API
description: 通过高德POI相关API获取加油站信息
---

基于高德**搜索POI 2.0** Web API，获取指定区域的POI信息，并使用folium进行可视化。

## 高德API
参照[文档](https://lbs.amap.com/api/webservice/guide/api/newpoisearch)说明，进行服务key申请。

## 通过关键词检索POI
### 载入依赖包
``` python
from urllib.parse import quote
from urllib import request
import json
import pandas as pd
import math
import folium
from folium.plugins import MarkerCluster
```

### 关键函数
通过分页遍历形式获取全部POI。
``` python
# 单页获取pois
def getpoi(region, keywords, pagenum):
    req_url = poi_search_url + "?key=" + amap_web_key + '&keywords=' + quote(
        keywords) + '&region=' + region + '&citylimit=true' + '&page_size=25' + '&page_num=' + str(
        pagenum) + '&output=json'
    with request.urlopen(req_url) as f:
        re = f.read()
        re = re.decode('utf-8')
    return re

# 根据城市名称和分类关键字获取poi数据
def getpois(region, types):
    i = 1
    poi_list = []
    while True:
        re = getpoi(region, types, i)
        re = json.loads(re)
        if re['count'] == '0':
            break
        for poi in re['pois']:
            poi_list.append(poi)
        i = i + 1
    return poi_list
```
### 执行
区域编码[从此](https://lbs.amap.com/api/webservice/download)获取。
坐标转换(gcj02towgs84)实现方式[参见](https://mattzou.com/2019/03/25/Python-%E5%9D%90%E6%A0%87%E8%BD%AC%E6%8D%A2/)。
``` python
# 初始参数
amap_web_key = ''
poi_search_url = "http://restapi.amap.com/v5/place/text"

# 指定区域编码与关键字
pois_area = pd.DataFrame(getpois('120116', '加油站'))
# 提取经纬度
pois_area[['longitude', 'latitude']] = pois_area['location'].str.split(',', 1, expand=True).astype(float)
# 经纬度转换
pois_area[['longitude_wgs84', 'latitude_wgs84']] = pois_area.apply(gcj02towgs84, axis=1,
                                                                     args=('longitude', 'latitude'),
                                                                     result_type="expand")
# folium地图可视化
m = folium.Map([38.97, 117.6],
               zoom_start=10)

marker_cluster = MarkerCluster().add_to(m)
# 遍历内容添加进入MarkerCluster
for _, r in pois_area1.iterrows():
    folium.Marker([r['latitude_wgs84'], r['longitude_wgs84']], popup=folium.Popup(
        html=folium.Html("""{}</br>{}</br>""".format(r['name'], r['address']), script=True),
        parse_html=True,
        max_width=3000,
        )).add_to(marker_cluster)
# 保存交互地图
output_file = "map_fuel.html"
m.save(output_file)
# 浏览器打开
webbrowser.open(output_file, new=2)
```
## 效果
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/Traffic/fuelstationmap.jpg/pic)