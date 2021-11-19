---
title: 城市公交线路获取
layout: post
author: MattZou
updated: 2021-05-07
date: 2021-05-06 11:55:42
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/Traffic/%E5%8C%97%E4%BA%AC%E6%80%BB%E5%9B%BE.jpg/bg
categories: 交通
tags:
- 高德API
description: 基于高德API的城市公交系统基础数据获取与展示
---

在城市交通研究中，公共交通系统是重要研究对象，其中公交地铁线路数据是研究的基础。由于公交线路会定期调整，因此通过服务商API动态获取线路信息是这类数据可靠的获取方式。

## 公交线路名称
### 思路
公交线路名称获取通过[8684网站](https://www.8684.cn/)查询解析获取[^1]，网站线路涵盖全，解析方便。

### 工具
解析工具采用`BeautifulSoup`，`BeautifulSoup`解析器区别如下[^2]：

| 解析器 	| 使用方法 	| 优势 	| 劣势 	|
|-	|-	|-	|-	|
| python标准库 	|BeautifulSoup(markup,"html.parser")| python内置标准库<br>执行速度适中<br>文档纠错能力强 	| python2.7.3以前的版本容错能力差 	|
| lxml HTML解析器 	|BeautifulSoup(markup,"lxml")| 速度快<br>文档纠错能力强 	| 需要安装C语言库 	|
| lxml xml解析器 	|BeautifulSoup(markup,["lxml","xml"])| 速度快<br>唯一的支持解析的xml的解析器 	| 需要安装C语言库 	|
| html5lib 	|BeautifulSoup(markup,"html5lib")| 最好的容错性<br>以浏览器的方式解析文档<br>生成html5格式的文档 	| 速度慢<br>不宜懒外部库 	|

这里解析器采用`lxml`，需先安装
``` 
    conda install lxml
```

### 代码
线路名称获取方式如下
``` python
# -*- coding: utf-8 -*-
# @Author  : MattZou
# @File    : BusLineName.py
# @Software: PyCharm
# -*-----------------*-

# Packages
import requests
import pandas as pd
from bs4 import BeautifulSoup
import time

# 列表解析
def list_parse(url: str):
    # 请求页面
    home_html = requests.get(url, headers=headers)
    # bs解析，使用lxml解析器
    Soup = BeautifulSoup(home_html.text, 'lxml')
    # 分类列表提取
    list_div = Soup.find_all('div', class_='list')
    # 以数字开头
    num_div = list_div[0].find_all('a')
    # 以汉字/字母开头
    char_div = list_div[1].find_all('a')

    return num_div, char_div


# 解析线路
def html_parse(div_list: list, url: str):
    parsed_list = []
    for a in div_list:
        try:
            # 链接提取
            href = a['href']
            html = url + href
            temp_html = requests.get(html, headers=headers)
            # 解析器
            Soup = BeautifulSoup(temp_html.text, 'lxml')
            # 下一级div
            cc = Soup.find('div', class_='cc-content').find_all('div')[-1]
            cc_a = cc.find_all('a')
            # 遍历添加
            for sub_a in cc_a:
                title = sub_a.get_text()
                parsed_list.append(title)
        except Exception as e:
            print('Network Error:', e)
            continue
    
    return parsed_list


if __name__ == "__main__":
    t0 = time.time()
    # 城市名称拼音
    city_name = "zhengzhou"
    # 查询城市网站地址
    home_page = 'https://{}.8684.cn/'.format(city_name)
    # 定义请求头信息
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
                      'Chrome/90.0.4430.93 Safari/537.36 Edg/90.0.818.51'}
    # 初始化名称列表
    name_list = []

    # 以数字开头
    name_list += html_parse(list_parse(home_page)[0], home_page)
    # 以汉字/字母开头
    name_list += html_parse(list_parse(home_page)[1], home_page)

    # 输出文件为csv
    name_pd = pd.DataFrame(name_list)
    name_pd.to_csv('./{}_bus_name.csv'.format(city_name), index=False, header=["Name"], encoding='utf8')

    t1 = time.time()
    print("运行用时：%.2fs" % (t1 - t0))
```

## 线路轨迹及站点
### 思路
基于采集到的公交名称，应用高德API提取每路公交停站点及线路轨迹。

### 代码
``` python
import requests
import json
import pandas as pd
import time


def get_line(key, city_name_pinyin, line_name):
    # 生产请求地址
    url = 'https://restapi.amap.com/v3/bus/linename?s=rsv3&extensions=all&key={}&output' \
          '=json&city={}&offset=1&keywords={}&platform=JS'.format(key, city_name_pinyin, line_name)
    # 获取数据
    re_text = requests.get(url).text
    re_json = json.loads(re_text)
    # noinspection PyBroadException
    try:
        # 解析公交线路部分信息
        line_info = {'line_name_short': line_name,
                     'line_name': re_json['buslines'][0]['name'],
                     'start_stop': re_json['buslines'][0]['start_stop'],
                     'end_stop': re_json['buslines'][0]['end_stop'],
                     'distance': re_json['buslines'][0]['distance'],
                     'station': int(re_json['buslines'][0]['busstops'][-1]['sequence'])}
        # 获取沿途站点站名和对应坐标
        station_name = []
        station_coords = []
        for st in re_json['buslines'][0]['busstops']:
            station_name.append(st['name'])
            station_coords.append(st['location'])
        line_info['station_name'] = station_name
        line_info['station_coords'] = station_coords
        line_info_pd = pd.DataFrame(line_info)
        line_info_pd[['longitude', 'latitude']] = line_info_pd['station_coords'].str.split(',', 1, expand=True)

        # 获取线路轨迹
        polyline = re_json['buslines'][0]['polyline']
        line_info_route = {'line_name_short': line_name, 'station_coords': polyline.split(";"), 'line_name': re_json['buslines'][0]['name']}
        route = pd.DataFrame(line_info_route)
        route[['longitude', 'latitude']] = route['station_coords'].str.split(',', 1, expand=True)
        return line_info_pd, route

    except Exception as e:
        print('Line：{} Not Find'.format(line_name))


if __name__ == "__main__":
    t0 = time.time()
    # 城市名称拼音
    city_name = "zhengzhou"
    # 读取线路名称文件
    name_pd = pd.read_csv('./{}_bus_name.csv'.format(city_name))
    # AppKey
    app_key = ''
    # 初始化结果集
    line_info_pd_list = []
    route_list = []
    # 逐条遍历
    for index, row in name_pd.iterrows():
        try:
            line_info_pd_temp, route_temp = get_line(app_key, city_name, row['Name'])
            line_info_pd_list.append(line_info_pd_temp)
            route_list.append(route_temp)
        except Exception as e:
            continue
    # 结果合并
    line_info_pd = pd.concat(line_info_pd_list, axis=0)
    route_pd = pd.concat(route_list, axis=0)
    # 结果输出
    line_info_pd.to_csv('./{}_bus_line_info.csv'.format(city_name))
    route_pd.to_csv('./{}_bus_line_route.csv'.format(city_name))

    t1 = time.time()
    print("运行用时：%.2fs" % (t1 - t0))
```

## 可视化
依赖`Geopandas`和`shapely`包实现Point和Line对象构建，创建GeoDataFrame[^3]，并使用`folium`创建交互地图。同时导出shp文件用以后续分析。

### 代码
``` python
import pandas as pd
import geopandas as gpd
from shapely.geometry import Point
from shapely.geometry import LineString
import folium
import matplotlib.pyplot as plt
import webbrowser

city_name = 'zhengzhou'

# 生成Point图层
def csv_to_points(to_shp = True):
    df = pd.read_csv('./{}_bus_line_info.csv'.format(city_name))
    gdf = gpd.GeoDataFrame(df, crs="EPSG:4326", geometry=gpd.points_from_xy(df['longitude'], df['latitude']))
    if to_shp:
        gdf_shp = gpd.GeoDataFrame(df, crs="EPSG:4326", geometry=gpd.points_from_xy(df['latitude'], df['longitude']))
        gdf_shp.to_file('./{}_bus_line_info.shp'.format(city_name), encoding='gbk')
    return gdf


# 生成Line图层
def csv_to_lines(to_shp = True):
    df = pd.read_csv('./{}_bus_line_route.csv'.format(city_name))
    # 按线路名称聚合
    dataGroup = df.groupby('line_name_short')
    # 构造数据
    attribute_table = []
    geometry = []
    for name, group in dataGroup:
        # 分离出属性信息，取每组的第1行前5列作为数据属性
        attribute_table.append(group.iloc[0,1:4])
        # 把同一组的点打包到一个list中
        line = LineString([xy for xy in zip(group.longitude, group.latitude)])
        geometry.append(line)
    # 点转线
    gdf = gpd.GeoDataFrame(attribute_table, crs="EPSG:4326", geometry=geometry)
    if to_shp:
        gdf.to_file('./{}_bus_line_route.shp'.format(city_name), encoding='gbk')
    return gdf


# 显示地图
points = csv_to_points()
lines = csv_to_lines()

# 底图
m = folium.Map([34.8, 113.7], zoom_start=10,  tiles='CartoDB positron')

# 线图层
folium.Choropleth(
    lines[lines.geometry.length > 0.001],
    line_weight=3,
    line_color='blue'
).add_to(m)

# 点图层
marker_cluster = MarkerCluster().add_to(m)
for _, r in points.iterrows():
    folium.Marker([r['latitude'], r['longitude']], popup=r['line_name_short']).add_to(marker_cluster)

output_file = "map.html"
m.save(output_file)
# open in new tab
webbrowser.open(output_file, new=2)
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/Traffic/folium_bus.jpg/pic)

## Reference
[^1]: [Python：利用高德API获取公交路线并可视化](https://blog.csdn.net/sinat_36226553/article/details/104948734)
[^2]: [爬虫：python之BeautifulSoup(lxml)](https://blog.csdn.net/zhangzejia/article/details/79658221)
[^3]: [Creating a GeoDataFrame from a DataFrame with coordinates](https://geopandas.readthedocs.io/en/latest/gallery/create_geopandas_from_pandas.html)
