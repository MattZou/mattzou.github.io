---
title: ggplot2 分面绘图
layout: post
author: MattZou
updated: 
date: 2019-07-03
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/ggplot2-facet.png/bg
categories: 软件使用
tags:
- R
- ggplot2
description: 主要介绍ggplot2里Facetting设置
---

主要介绍ggplot2里Facetting使用要点。

`Facetting`用于将图以面板矩阵的形式分成多个子图进行绘制，主要通过以下函数实现[^1]。
- `facet_grid()`
- `facet_wrap()`

一些常规设置参考此文[^2]，下面是我使用时调整的一些内容。

## 面板标签模块设置
### Label替换
这部分内容在`facet_grid/warp()`里调整
  ``` r
  # 构造替换字典
  # 新label
  supp.labs <- c("Orange Juice", "Vitamin C")
  # 原label
  names(supp.labs) <- c("OJ", "VC")

  # 在labeller中进行替换
  p + facet_grid(
    . ~ supp, 
    labeller = labeller(supp = supp.labs)
    )
  ```

### 字体及背景色
这部分在`theme()`里调整，与分面相关的属性以**strip**开头。
  ``` r
   theme(
        # 文字样式调整
        strip.text.x = element_text(
        size = 12, color = "red", face = "bold.italic"),
        # 标签背景色调整，fill表示填充色，color表示边框颜色
        strip.background = element_rect(
        color="black", fill="#FC4E07", size=1.5, linetype="solid"))
  ```

### 为不同子图增加标注
从`facet_`实现考虑，分页字段可以看作子图索引（二维），因此通过创建包含分页字段的标注子数据集，在绘图时就可以在指定子图上加入特定标注[^3]。
``` r
# 载入包
library(ggplot2)
library(Rmisc)

# 载入数据
butter <- read.table("xxx", header = TRUE)

# summary数据
buttersum <- summarySE(data = butter, measurevar = "winglen", 
                     groupvars = c("spp", "sex", "region"))

# 为标注创建数据集，包含region这个分面字段，这一步是关键
anno <- data.frame(x1 = c(1.75, 0.75), x2 = c(2.25, 1.25), 
                   y1 = c(36, 36), y2 = c(37, 37), 
                   xstar = c(2, 1), ystar = c(38, 38),
                   lab = c("***", "**"),
                   region = c("North", "South"))
```
标注形态如下
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/diag2-14.png)
``` r
# 绘制图像，选择region作为分面
ggplot(data = buttersum, aes(x = spp, y = winglen)) +
  geom_point(aes(shape = sex), position = position_dodge(width = 1), size = 2)+
  scale_shape_manual(values = c(1, 19), labels = c("Female", "Male") )+
  geom_errorbar(aes(group = sex, ymin = winglen - se, ymax = winglen + se), 
                width = .2, position = position_dodge(width = 1)) +
  geom_text(data = anno, aes(x = xstar,  y = ystar, label = lab)) +
  geom_segment(data = anno, aes(x = x1, xend = x1, 
           y = y1, yend = y2),
           colour = "black") +
  geom_segment(data = anno, aes(x = x2, xend = x2, 
           y = y1, yend = y2),
           colour = "black") +
  geom_segment(data = anno, aes(x = x1, xend = x2, 
           y = y2, yend = y2),
           colour = "black")+
  facet_grid(. ~ region)
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/annofacet-15.png)

## Reference
[^1]: [Facetting](https://ggplot2.tidyverse.org/reference/index.html#section-facetting)
[^2]: [ggplot2 facet : split a plot into a matrix of panels](http://www.sthda.com/english/wiki/ggplot2-facet-split-a-plot-into-a-matrix-of-panels)
[^3]: [Adding different annotation to each facet in ggplot](https://buzzrbeeline.blog/2018/11/06/adding-different-annotation-to-each-facet-in-ggplot/)
