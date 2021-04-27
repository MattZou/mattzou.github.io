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
搭配[RStudio](https://www.rstudio.com/products/rstudio/)食用更佳。

### 功能速览
1. Rstudio提供一系列的[Cheatsheets](https://www.rstudio.com/resources/cheatsheets/)用于快速入门及核心功能速查，ggplot2部分可[下载](https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf)。
2. 各函数详细参数及示例参见[Reference](https://ggplot2.tidyverse.org/reference/index.html)。
3. ggplot2还支持[拓展包](https://exts.ggplot2.tidyverse.org/gallery/)，针对特殊功能，如[动态图片](https://gganimate.com/)、[主题设定](https://github.com/jrnold/ggthemes)、[图表注释](https://github.com/aphalo/ggpmisc/)、[增强型统计图表]([ggstatsplot](https://github.com/IndrajeetPatil/ggstatsplot))等，拓展包提供了更好的封装，简化使用。

### 可视化理念
- **作者意图**
> All plots are composed of the **data**, the information you want to visualise, and a **mapping**, the description of how the data's variables are mapped to aesthetic attributes.[^1]
> [Hadley Wickham](http://hadley.nz/)
- **我的理解**
gg:  **Grammar of Graphics**，ggplot2，把图分为数据，几何图形，图形属性等几个部分，把图的各个部分定义为组件形式，组件实现数据与几何对象的映射，通过图形属性定义组件之间的组合形式进行绘图。

## Tricks and Bugs

### Framework
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

以下内容为我在日常使用中调整和配置的tricks。
### Geoms
- geom_point
查看设置点的形状
``` r
ggpubr::show_point_shapes()
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/pointtype.png/pic)

- geom_line
查看设置线的形状
``` r
ggpubr::show_line_types()
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/linetype.jpg/pic)


### Scales
- [ggplot2 坐标轴调整](https://mattzou.com/2019/11/23/ggplot2-Axis/)

- [ggplot2 使用expression添加公式与数学符号](https://mattzou.com/2020/07/16/ggplot2-Expression/)

- [ggplot2 双坐标轴实现](https://mattzou.com/2019/11/23/ggplot2-Dual-Axis/)

### Themes
用于调整`theme(...)`中的内容。

- 背景色
``` r
# 无填充则设置为 element_blank()
# 绘图区
panel.background = element_rect(fill, color, ...),
# 绘图区外侧
plot.background = element_rect(fill, color, ...)
```

- 网格线
``` r
# 主网格
panel.grid.major = element_line(colour, size, ...)
# 次网格
panel.grid.minor = element_line(colour, size, ...)
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
[ggplot2 添加注释](https://mattzou.com/2020/10/21/ggplot2-Annotation/)


## Note
- ggplot2 提供了强大的自定义功能，可以通过长期使用形成适应自己研究体系的绘图模板，方便绘图的标准化，未来使用时可将更多精力放在数据处理上。

- 但更高的灵活度意味着简单功能可能也需要复杂代码实现（或者根本实现不了，需要绕路😂），如果折腾精力不够，建议简单图还是使用传统绘图软件。

- 对于特别复杂的图，各种高端炫酷的以及3D类还是建议AI+PS（懂得都懂😏）、地理空间类则建议使用`Python`的[Geopandas](https://geopandas.org/)，机器学习类的图也是`Python`提供的轮子更多。

> ggplot2 虽好， 可不要贪杯🍻哦

## Acknowledgements 
   <a href="https://mattzou.com/2021/04/15/R-ggplot2/#Acknowledgements" class="card-body hover-with-bg" target="_blank" rel="noopener">
     <div class="card-content">
      <div class="link-avatar my-auto">
        <img src="https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/%E7%8C%B4%E5%AD%90%E5%A5%B3%E7%8E%8B.jpg" alt=""
             onerror="this.onerror=null; this.srcset=null; this.src=''"/>
      </div>
       <div class="link-text">
         <div class="link-title">🐒👑</div>
         <div class="link-intro"></div>
       </div>
     </div>
   </a>
   <a href="https://mattzou.com/2021/04/15/R-ggplot2/#Acknowledgements" class="card-body hover-with-bg" target="_blank" rel="noopener">
     <div class="card-content">
      <div class="link-avatar my-auto">
        <img src="https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/%E6%96%87%E4%BB%B6%E4%BC%A0%E5%81%B7%E5%8A%A9%E7%90%86.jpg" alt=""
             onerror="this.onerror=null; this.srcset=null; this.src=''"/>
      </div>
       <div class="link-text">
         <div class="link-title">文件传偷助理</div>
         <div class="link-intro"></div>
       </div>
     </div>
   </a>


## Reference
[^1]: [ggplot2: elegant graphics for data analysis](https://ggplot2-book.org/index.html)
[^2]: [Function Reference](https://ggplot2.tidyverse.org/reference/index.html)
[^3]: [r - 默认的ggplot2绘图边距是多少?](https://www.coder.work/article/6542920)
[^4]: [R语言基础绘图之边距参数](https://www.jianshu.com/p/5fbaf17d9aee)

























