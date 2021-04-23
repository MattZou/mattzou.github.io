---
layout: post
title: ggplot2 总结
author: MattZou
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/logo.png/bg
banner_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/README-example-1.png/bg
date: 2021/04/15
updated: 2021/04/17
categories: 软件使用
tags:
- R
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
gg:  **Grammar of Graphics**，ggplot2，把图分为数据，几何图形，图形属性及部分，把图的各个部分定义为组件形式，组件实现数据与几何对象的映射，通过图形属性定义组件之间的组合形式进行绘图。
## Tricks and Bugs


## Reference
[^1]: [ggplot2: elegant graphics for data analysis](https://ggplot2-book.org/index.html)


























