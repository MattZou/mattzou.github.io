---
layout: post
title: ggplot2 总结
author: MattZou
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/logo.png/bg
banner_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/README-example-1.png/bg
date: 2021/04/15
updated:
categories:
- 目录
tags:
- ggplot2
description:
---

> ggplot2使用总结，各种坑和技巧
<!-- more -->
## What is ggplot2

[**ggplot2**](https://ggplot2.tidyverse.org/index.html)是[tidyverse](https://www.tidyverse.org/)家族中进行数据可视化的核心package
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/tidyverse.jpg/pic)

## How to use ggplot2
### 安装
``` r
install.packages("tidyverse")
```
搭配[Rstudio](https://www.rstudio.com/products/rstudio/)食用更佳。

### 功能速览
1. Rstudio提供一系列的[Cheatsheets](https://www.rstudio.com/resources/cheatsheets/)用于快速入门及核心功能速查，ggplot2部分可直接[下载](https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf)。
2. 各函数详细参数及示例参见[Reference](https://ggplot2.tidyverse.org/reference/index.html)。
3. ggplot2还支持[拓展包](https://exts.ggplot2.tidyverse.org/gallery/)，针对特殊功能，如[动态图片](https://gganimate.com/)、[主题设定](https://github.com/jrnold/ggthemes)、[图表注释](https://github.com/aphalo/ggpmisc/)、[增强型统计图表]([ggstatsplot](https://github.com/IndrajeetPatil/ggstatsplot))等，拓展包提供了更好的封装，简化使用。

### 可视化理念
- **作者意图**
> All plots are composed of the **data**, the information you want to visualise, and a **mapping**, the description of how the data’s variables are mapped to aesthetic attributes.[^1]
> [Hadley Wickham](http://hadley.nz/)
- **我的理解**
gg:  **Grammar of Graphics**，ggplot2，把图分为数据，几何图形，图形属性等几个部分，把图的各个部分定义为组件形式，组件实现数据与几何对象的映射，通过图形属性定义组件之间的组合形式进行绘图。

## Tricks and Bugs
个人习惯按以下核心框架绘图。
``` r
p <- 
 # 创建空白ggplot对象
 ggplot() +
 # 添加一个或多个几何对象
 # 个人不太喜欢把data和aes放在ggplot()里全局设置
 # 如果多个几何对象分别使用不同数据源，指定全局设置会造成混乱
 geom_point(data = , aes(x = , y = ), ...) + 
 # 设置坐标轴标题
 labs(x = , y = , ...) + 
 # 设置坐标轴缩放
 scale_x_continuous(limits = , breaks = , ...) + 
 # 添加标注
 annotate(...) + 
 # 设置主题样式
 theme_bw(...) + 
 # 覆写部分主题配置
 theme(...)
```
各类图都可以在此模板基础上进行调整与自定义，详细配置建议参考[^2]。

以下内容为我在日常使用调整配置的tricks。
### Geoms
#### geom_point
查看设置点的形状
``` r
ggpubr::show_point_shapes()
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/pointtype.png/pic)


### Scales
#### label
[ggplot2 使用expression添加公式与数学符号](https://mattzou.com/2020/07/16/ggplot2-Expression/)

#### axis

### Themes
用于调整`theme(...)`中的内容。

- 轴标题旋转
``` r
# angle定义旋转角度
# 由于旋转后文字会与坐标轴干涉冲突，可设置vjust或hjust偏移量调整
axis.title.y = element_text(angle = 0, vjust = 0.5)
```

- 绘图边距
查看默认绘图边距[^3]，其他边距内容参见[^4]
``` r
# 默认主题
theme_grey()$plot.margin
[1] 5.5pt 5.5pt 5.5pt 5.5pt

# 其他主题（推荐）
theme_get()$plot.margin 
```

- [ggplot2 调整图例](https://mattzou.com//2020/07/14/ggplot2-Legend/)

### Color
[ggplot2 颜色配置](https://mattzou.com/2019/04/21/ggplot2-Color/)

### Font
绘图使用`Times New Roman`字体。
1. 定义字体样式
``` r
windowsFonts(RMN = windowsFont("Times New Roman"))
```
2. 在需要设置字体的位置设置`family`属性
``` r
family = "RMN"
```

### Facetting
[ggplot2 分面绘图](https://mattzou.com/2019/07/03/ggplot2-Facet/)

### Annotations


## Reference
[^1]: [ggplot2: elegant graphics for data analysis](https://ggplot2-book.org/index.html)
[^2]: [Function Reference](https://ggplot2.tidyverse.org/reference/index.html)
[^3]: [r - 默认的ggplot2绘图边距是多少?](https://www.coder.work/article/6542920)
[^4]: [R语言基础绘图之边距参数](https://www.jianshu.com/p/5fbaf17d9aee)

























