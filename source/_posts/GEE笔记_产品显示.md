---
layout: post
title: GEE常用卫星产品载入及显示
author: MattZou
date: 2019/11/30
updated: 2021/04/11
categories: [软件使用]
tags: [GEE]
description:
---

> 在GEE平台code editor里显示卫星产品
<!-- more -->
在GEE中，部分资产可以通过[Explorer](https://explorer.earthengine.google.com/#workspace)进行预览，多数情况下下在[Editor](https://code.earthengine.google.com/)里查看更为方便。

## Landsat 8 
```js
var l8 = ee.ImageCollection("LANDSAT/LC08/C01/T1"),
    roi = 
    ee.Geometry.Polygon(
        [[[119.23097080607909, 36.93905749319195],
          [119.23097080607909, 32.21992180906591],
          [124.09242100139159, 32.21992180906591],
          [124.09242100139159, 36.93905749319195]]], null, false),

var Data_Collection = l8.filterDate('2016-05-01','2016-08-30');
//选择云量最少的影像组合
var Select_Collection=  ee.Algorithms.Landsat.simpleComposite(Data_Collection).clip(roi)
//载入图层
Map.addLayer(Select_Collection,{bands: ["B4","B3","B2"],gamma: 1.5,max: 108,min: 15},'True-Color');
Map.centerObject(roi, 9); 
```

## MODIS
```js
// 选择数据源，并定义范围 roi
var mod500 = ee.ImageCollection("MODIS/006/MYD09A1"),
    roi = 
    ee.Geometry.Polygon(
        [[[118.96562188688824, 36.08745610383932],
          [118.96562188688824, 34.000935416392934],
          [121.29472344938824, 34.000935416392934],
          [121.29472344938824, 36.08745610383932]]], null, false);

var image_mod500 = ee.Image(
     mod500.filterDate('2016-05-01','2016-08-30'd)
    .first()
).clip(roi).toFloat();

// Select the QA band
var QA = image_mod500.select('StateQA');
// Get the cloud_state bits and find cloudy areas.
var cloud = getQABits(QA, 0, 1, 'cloud_state')
                    .expression("b(0) == 1 || b(0) == 2");
// Create a mask that filters out  cloudy areas.
var mask = cloud.not();

//定义显示参数
var visParams_color = {bands:['sur_refl_b01','sur_refl_b04','sur_refl_b03'],min:0,max:3000,gamma:1.3};
//载入图层
Map.addLayer(image_mod500.updatemask(mask), visParams_color, 'mod500_color');
```

## 参考
1. [知乎用户 **无形的风**][1]
[1]:https://www.zhihu.com/people/li-shi-wei-36
2. [知乎专栏][2]
[2]:https://www.zhihu.com/column/c_123993183
