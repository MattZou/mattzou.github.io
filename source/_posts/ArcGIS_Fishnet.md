---
layout: post
title: ArcGIS创建网格
author: MattZou
date: 2016/2/28 18:09:22 
categories: ArcGIS
tags: 
- 网格
- Fishnet
---

在排放展示和统计过程中，需要按照网格的方式进行划分，需要建立网格。
<!-- more -->
### 问题

-  创建网格，覆盖现有图层要素
-  可调整网格大小
-  获取网格中心坐标

### 方法

在ArcGIS中，应用**系统工具箱**中的``` Create Fishnet ```工具来创建网格。

### 工具位置

在**Catalog**中选择`Toolboxes`，找到`System Toolboxes`，其中`Data Management Tools`工具箱中点开`Feature Class`一项就可以看见`Create Fishnet`工具。

![create fishnet 1](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/create%20fishnet.1.jpg)

### 步骤

1. 选择导出文件位置；
2. **可选项**选择现有图层为模板，自动生成网格边界范围
3. 修改自动生成范围数值；
4. 手动输入网格边界范围，如果*2*步骤选择了模板图层，会自动生成边界数值；
5. 设置网格宽度及高度**数值单位与图层坐标系统匹配**；
6. 设置网格行数和列数**如果选择了模板图层，最好都设置为0，会自动根据以上输入的网格尺寸及图层范围计算行列数，不用自己计算**；
7. **可选项**生成每个网格中心点图层，这个选项在之后定位网格位置时有用处；
8. 选择网格类型，`POLYLINE`及`POLYGON`分别是生成线要素网格或生成面要素网格；

> 以天津市为模板图层，生成一个覆盖天津市的1Km*1Km的网格，参数选择如下图所示：

![create fishnet 2](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/create%20fishnet.2.jpg)

### 结果

> 设置好参数后生成的网格图层如下所示：

![create fishnet 3](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ArcGIS/create%20fishnet.3.JPG)

以上就完成了网格图层的生成，接下来就可以用生成的网格对其他图层进行切分等操作了。

----------