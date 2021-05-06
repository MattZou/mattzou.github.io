---
title: ggplot2 调整图例
layout: post
author: MattZou
updated: 
date: 2020-07-14
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/ggplot2-legend-example.png/bg
categories: R
tags:
- R
- ggplot2
description: 主要介绍ggplot2里Legend部分设置
---

主要介绍ggplot2里Legend部分设置

ggplot2提供`guide_legend()`设置图例[^1]，更多参数可参考。

## 常用设置
以下设置在`theme()`中配置，常规设置请参考[此文](http://www.sthda.com/english/wiki/ggplot2-legend-easy-steps-to-change-the-position-and-the-appearance-of-a-graph-legend-in-r-software)。
``` r
# 图例位置与文字样式调整
legend.position = |"left", "top", "right", "bottom", "none"|
legend.text = element_text(...),
legend.title = element_text(...)
```

我自己绘图时还对以下内容进行设置。

### 图例尺寸与边距
``` r
# 图例长宽
 legend.key.size=unit(3,'mm')
 legend.key.width=unit(10,'mm')

# 图例与图的距离或两个图例之间距离
 legend.margin=margin(b = -0.3, unit='cm')
```

### 图例文字与颜色映射
对于`aes()`产生的图例，可以使用`scale_color_identity`或`scale_color_manual`两种方式自定义图例显示名称与颜色的映射关系[^2]

1. `aes`定义颜色描述，通过label指定文字描述
``` r
ggplot(mtcars, aes(mpg, hp) ) +
     geom_point() +
     geom_smooth(method = "lm", se = FALSE, aes(color = "black") ) +
     geom_smooth(method = "lm", formula = y ~ poly(x, 2), se = FALSE, aes(color = "red")) +
     geom_smooth(method = "lm", formula = y ~ poly(x, 3), se = FALSE, aes(color = "blue")) +
     scale_color_identity(name = "Model fit",
                          breaks = c("black", "red", "blue"),
                          labels = c("Linear", "Quadratic", "Cubic"),
                          guide = "legend")
```
2. `aes`定义文字描述，通过value建立颜色映射
``` r
ggplot(mtcars, aes(mpg, hp) ) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE, aes(color = "Linear") ) +
  geom_smooth(method = "lm", formula = y ~ poly(x, 2), se = FALSE, aes(color = "Quadratic")) +
  geom_smooth(method = "lm", formula = y ~ poly(x, 3), se = FALSE, aes(color = "Cubic")) +
  scale_color_manual(name = "Model fit",
                     breaks = c("Linear", "Quadratic", "Cubic"),
                     values = c("Cubic" = "blue", "Quadratic" = "red", "Linear" = "black"))
```

### 图例顺序
对于多个图例(size, shape, colour, alpha, fill等)，多个图例顺序调整方式如下
``` r
# nrow调节行列数，order调节多个图例的顺序
p + guides(colour = guide_legend(nrow = 1, order = 1), 
            fill = guide_legend(nrow = 1, order = 2))
```

## Open air风玫瑰图图例Label定义
通过加入`key = list(labels = c())`进行定义，替换现有label[^3]。
``` r
library(openair)
windRose(mydata, ws="ws", wd="wd", breaks=c(0,1.5,3.3,5.4,7.9,10.7), 
         auto.text= FALSE, paddle = FALSE, annotate = FALSE,
         key = list(labels = c()))
```

## Reference
[^1]: [Legend guide](https://ggplot2.tidyverse.org/reference/guide_legend.html)
[^2]: [Creating legends when aesthetics are constants in ggplot2](https://aosmith.rbind.io/2018/07/19/manual-legends-ggplot2/)
[^3]: [Changing the legend labels in a windrose from R openair](https://databasefaq.com/index.php/answer/112135/r-legend-changing-the-legend-labels-in-a-windrose-from-r-openair)
