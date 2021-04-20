---
layout: post
title: ArcGIS路网网格划分
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/features-hero_maps-and-data.png
author: MattZou
date: 2016/10/17 21:23:15 
categories: 软件使用
tags: 
- ArcGIS
- 路网切分

---

在已有网格的情况下，用网格将道路切断，以便建立道路和网格的对应关系。

<!-- more -->

### 问题

现有北京[网格图层（线要素）][1]以及北京**路网图层**，要用网格线切分道路。

### 方法
将网格图层中的全部要素复制到路网图层，选用``` Planarize Lines```工具，将相交线打断，完成切分后通过要素筛选，将参与切分的网格线删除，剩下的路网就是切分好的了，接着通过[要素映射][2]建立道路与网格的映射关系，道路网格划分就完成了。 

### 工具位置

图层进入编辑状态后，**Advanced Editing** 工具栏上，点击即运行。

![路网切分 1](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/%E8%B7%AF%E7%BD%91%E5%88%87%E5%88%861.jpg/pic) 

### 步骤

1.在**路网图层**上[启动编辑][3];

2.[全选][4]**网格图层**要素，``` Ctrl + C``` ，然后```Ctrl + V``` 到**路网图层**;

3.在合并生成的新路网图层中全选要素，在**Advanced Editing** 工具栏上点击[Planarize Lines][5]工具；

4.打开切分完成后的图层属性表，通过网格图层特有属性，用[按属性选择][6]方法，将网格线要素选出，并删除，剩下的就是切分好的路网；


### 结果

> 道路均在网格线处被截断：

![路网切分 2](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/%E8%B7%AF%E7%BD%91%E5%88%87%E5%88%862.jpg/pic)

[1]: http://mattzou.github.io/2016/02/28/ArcGIS_Fishnet_2016-02-28/#
[2]: http://mattzou.github.io/2016/03/03/ArcGIS_%E8%A6%81%E7%B4%A0%E6%98%A0%E5%B0%84_2016-03-03/#
[3]: http://resources.arcgis.com/zh-cn/help/main/10.2/index.html#/na/01m60000005p000000/
[4]: resources.arcgis.com/zh-cn/help/main/10.2/index.html#/na/00s50000000w000000/
[5]: http://resources.arcgis.com/en/help/main/10.2/index.html#//01m800000012000000/
[6]: http://resources.arcgis.com/zh-cn/help/main/10.2/index.html#/na/00s500000021000000/

----------
