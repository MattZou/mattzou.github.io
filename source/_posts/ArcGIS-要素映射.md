---
layout: post
title: ArcGIS 要素映射关系建立
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/features-hero_directions.png/bg
author: MattZou
date: 2016/3/3 10:40:28 
updated: 2016/3/3 10:40:28 
categories: 软件使用
tags: 
- ArcGIS
- 映射关系

---

要素映射关系是工作中常遇到的问题，举个例子，比如一个面要素图层和一个点要素图层，要想知道每个点是落在哪个面之中，就需要建立一个点和面的映射关系。

<!-- more -->

### 问题

现有北京[网格图层][1]以及北京**环线图层**，要确定网格中每个格点处于几环。

### 方法
选用``` Join Data```工具，** Join data from another layer on spatial location **方法将两个图层的要素进行映射。 

### 工具位置

右键单击图层，选择``` Join and Relates ```，菜单中选``` Join ```。

![映射关系 1](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/%E6%98%A0%E5%B0%84%E5%85%B3%E7%B3%BB.1.jpg/pic) 

### 步骤

> 在目标图层也就是**网格图层**上单击右键，找到```  Join ```工具；

1. 选择** Join **类型，第一种是只连接两个图层属性表，第二种是根据空间位置将图层要素进行连接，同时连接属性表；
2. 选择连接对象，这里选择环线图层；
3. 选择连接```  Options ```，第二种方式可以实现我们所需要的映射关系，即落入相应环线要素的网格都将被赋予**环线图层**的属性，如果给**环线图层**的每个要素都进行标记，比如一环为```  ID ```字段为* 1 *，那么在连接后，落在一环内的网格将会增加一个字段```  ID ``` ，值为* 1 *；
4. 导出文件。

> 参数选择如下：

![映射关系 2](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/%E6%98%A0%E5%B0%84%E5%85%B3%E7%B3%BB.2.jpg/pic)

### 结果

> 生成的新图层如下所示：

![映射关系 3](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/%E6%98%A0%E5%B0%84%E5%85%B3%E7%B3%BB.3.JPG/pic)

打开图层属性表，可以看到新增的字段，标识网格所处的环线，通过``` selec by attributes ```高亮所有有映射关系的网格，和环线图层匹配正确，目的达到。

[1]: https://mattzou.com/2016/02/28/ArcGIS-Fishnet/#


----------
