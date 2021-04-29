---
title: ggplot2 添加注释
layout: post
author: MattZou
updated: 2020-10-21
date: 2020-10-21
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/unemp-pres-1.png/bg
categories: 软件使用
tags:
- R
- ggplot2
description: 主要介绍ggplot2添加图注释
---

主要介绍ggplot2添加图注释

## 使用annotate()
ggplot2 中提供各种灵活的方式添加注释，比如使用`geom_text, geom_rect, geom_line`等，但我更推荐使用`annotate()`进行标注。

`annotate()`使用`vectors`中的值进行几何对象数据映射，相比`geom_`类从**data frame**映射数据更加轻便[^1]。

## 标注类型
`annotate()`通过第一个参数指定标注类型：
> `"text"，"rect"，"segment"，"pointrange"，"curve"`，

## 实现方式
- 以坐标形式指定标注放置位置
``` r
p + annotate(
    geom = "curve", x = 4, y = 35, xend = 2.65, yend = 27, 
    curvature = .3, arrow = arrow(length = unit(2, "mm"))
  ) +
  annotate(geom = "text", x = 4.1, y = 35, label = "subaru")
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/curve_annotation-1.png/pic)

- 对于`"rect"`型标注，通过设置 `ymin/ymax` 为 `+/-Inf`，绘制背景分区样式[^2]
``` r
p + geom_rect(
    aes(xmin = start, xmax = end, fill = party), 
    ymin = -Inf, ymax = Inf, alpha = 0.2, 
    data = presidential
  ) + 
  scale_fill_manual(values = c("blue", "red"))
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/unemp-pres-1.png/pic)

- 在Facet[增加标注](https://mattzou.com/2019/07/03/ggplot2-Facet/#为不同子图增加标注)参考此文。

## Reference
[^1]: [Create an annotation layer](https://ggplot2.tidyverse.org/reference/annotate.html)
[^2]: [Annotations](https://ggplot2-book.org/annotations.html#annotations)