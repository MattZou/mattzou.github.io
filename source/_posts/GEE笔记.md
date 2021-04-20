---
layout: post
title: Google Earth Engine 教学视频笔记
author: MattZou
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/solution_earth.jpg/bg
banner_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/tools_laptop.jpg/bg
date: 2019/11/30
updated: 2021/04/11
categories: 
- [软件使用]
tags:
- [GEE]
- [遥感]
description:
---

> B站上GEE基础视频要点笔记
<!-- more -->

## 基础数据类型：
### String、Number
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee1.png/pic)
- 基础运算：拼接、替换、切分
- 位运算用于云层处理
### Array
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee2.png/pic)
### List
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee3.png/pic)
### Dictionary
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee4.png/pic)
- Add 添加内容，Insert 可以指定位置，map操作比较重要，循环使用

## 空间数据对象：
### Geometry
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee5.png/pic)
- 实现空间坐标变换，在[epsg](https://epsg.io/)网站查找epsg代码填入参数
- `Geometry.distance()`  用于测算最近两点之间的距离
### Feature
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee6.png/pic)
- `Feature.Area()` 求面积
### Feature Collection
-  相当于arcgis shp编辑
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee7.png/pic)
-  reduceToImage 矢量转栅格
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee8.png/pic)
-  toLIst 变成列表元素
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee9.png/pic)
### Image
- Mask  掩膜操作  类似裁剪，但不真实删除元素
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee10.png/pic)
-  `Clip(geometry)`  裁剪操作
- `Select(‘bands’)`  波段名称选择   slice()  按波段index选择
- `Addbands()` 加波段
- `Reproject()`  epsg:3857 常用网络地图坐标代号
- Rgb hsv 转换
- `where()`重新赋值  对小于4000的重赋值为0  可以剔除某些内容
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee11.png/pic)
- 数值判断，值域截取
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee12.png/pic)
- 自定义函数，计算NDVI时可用
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee13.png/pic)
- `Image.derivative()`高程进行x， y 微分 求坡度
- 纹理提取
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee14.png/pic)
- 临域操作，聚合替换
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee15.png/pic)
### Image Collection
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee16.png/pic)
- `Filter` 边界、时间段、数据属性筛选
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee17.png/pic)
- `mosaic()`实现多图叠加，将collection变一张image，重叠位置规则：后面取代前面
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee18.png/pic)
- `Select` 波段筛选并重命名，只能对同源数据操作
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee19.png/pic)
- 逻辑判断，Or 至少一次，and 多次
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee20.png/pic)
-  `unmixing`解决同物异谱或同谱异物
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee21.png/pic)

## 对象操作
### Dates
- 时间基本操作
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee22.png/pic)
- 时间表示方法
使用[`Unix timestamp`](https://www.unixtimestamp.com/)表示
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee23.png/pic)
- 时间段
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee24.png/pic)
- 时间段合并union
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee25.png/pic)

### Filters
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee26.png/pic)
- 筛选器操作步骤：
	1. 获取集合
	2. 创建筛选器  f = Filter.xxxx()
	3. 对集合执行筛选器 collections.filter(f)
- 内容筛选与获取
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee27.png/pic)
- 日期筛选
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee28.png/pic)
- 组合筛选
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee29.png/pic)
### Joins
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee30.png/pic)
- 左连接，定义筛选器筛选
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee31.png/pic)
- 影像产品结合
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee32.png/pic)
- saveAll 只链接属性，类似ArcGIS join中字段连接
- saveBest 参见guide
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee33.png/pic)

### Reducers
- 主要用于栅格数据分析
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee34.png/pic)
- 计数，countEvery 可以计算NA
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee35.png/pic)
- Reducers  max读取波段取最大， 或者多波段中位值
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee36.png/pic)
### Kernel
- 对某一个栅格周围元素进行操作
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee37.png/pic)
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee38.png/pic)
-  对象识别与边界提取
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee39.png/pic)
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/gee/gee40.png/pic)


## 参考

1. [遥感大数据平台 Google Earth Engine 教学视频][1]
[1]:https://www.bilibili.com/video/BV1Sb411p7TQ