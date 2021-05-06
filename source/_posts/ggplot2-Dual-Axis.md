---
title: ggplot2 双坐标轴实现
layout: post
author: MattZou
updated: 
date: 2019-11-23
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/thecode3-1.jpg/bg
categories: 软件使用
tags:
- R
- ggplot2
description: 主要介绍ggplot2里双坐标轴实现
---
按照ggplot2作者的思路，双y轴会降低图片的易读性，不推荐使用，所以ggplot2没有单独双y轴设置，这里有一系列关于双轴的讨论[^1][^2]。只能通过`scale_y_continuous()`与`sec.axis`属性迂回实现[^3]。


## 定义第二轴
`sec.axis()`是`scale_y_continuous()`的属性，`sec.axis()`并非构建全新的Y轴。 它只是通过数学变换由第一个Y轴构建第二个Y轴。因此对于第二轴数据，在绘图前需要先按合适比例缩放，以保证缩放后值域在主轴数据值域内，随后绘制第二轴几何图形**geom_x**，再通过`sec.axis()`里的`trans`进行逆运算还原第二轴实际数值显示[^4]。

具体实现如下：
``` r
# 定义缩放系数，系数根据左右轴的值域比例确定，找公约数，这样可以保证网格线对齐
coeff <- 10

ggplot(data, aes(x=day)) +
  # 主轴几何对象正常绘制
  geom_line( aes(y=temperature)) + 
  # 第二轴数据在绘制几何图形时进行缩放至主轴值域
  geom_line( aes(y=price / coeff)) + 
  scale_y_continuous(
    # 设置第一轴，左侧主轴
    name = "First Axis",
    # 增加右侧第二轴，并通过系数还原值显示
    sec.axis = sec_axis(~.*coeff, name="Second Axis")
  )
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/thecode3-1.png/pic)
这里有相关例子可以参考辅助理解[^5]。

## Reference
[^1]: [Dual-Scaled Axes in Graphs Are They Ever the Best Solution?](http://www.perceptualedge.com/articles/visual_business_intelligence/dual-scaled_axes.pdf)
[^2]: [A Study on Dual-Scale Data Charts](https://www.lri.fr/~isenberg/publications/papers/Isenberg_2011_ASO.pdf)
[^3]: [Specify a secondary axis](https://ggplot2.tidyverse.org/reference/sec_axis.html)
[^4]: [Dual Y axis with R and ggplot2](https://www.r-graph-gallery.com/line-chart-dual-Y-axis-ggplot2.html)
[^5]: [ggplot2: Secondary Y axis](https://whatalnk.github.io/r-tips/ggplot2-secondary-y-axis.nb.html)


