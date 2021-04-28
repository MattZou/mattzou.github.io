---
title: ggplot2 颜色配置
layout: post
author: MattZou
updated: 2020-08-31
date: 2019-04-21 23:17:39
index_img: https://www.r-graph-gallery.com/img/graph/274-map-a-variable-to-ggplot2-scatterplot.png
categories: 软件使用
tags:
- R
- ggplot2
description: 主要介绍ggplot2里颜色设置
---

ggplot2主要通过`FILL`和`COLOR`属性设置几何对象的颜色，可以直接指定某一种颜色，让颜色随变量自动配置或者使用调色板设置颜色[^1]。

## 色彩代码
对于单个颜色选择，可以参考R语言绘图颜色对照表，其中包含了几乎可以用到的颜色代码
<br>
{% pdf https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/ColorChart.pdf %}
<br>

## 调色板
对于连续色阶或者多种色彩搭配，官方建议使用`RColorBrewer`提供的调色板。
ggplot2原生支持通过设置`palette`来切换调色板[^2]，[这里](https://www.r-graph-gallery.com/38-rcolorbrewers-palettes.html)提供了预设`ColorBrewer`调色板代号及预览。
``` r
p + scale_color_brewer(palette = "PuOr")
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/thecode-1.png/pic)

另外一种官方原生支持的色板`Viridis`，[这里](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html)有预设色板。这个色板以连续颜色为主，也可以通过设置`discrete=TRUE`属性使用离散色阶。
``` r
p + scale_color_viridis(option="magma")
```
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/viridis.png/pic)

此外还可通过[ggsci](https://cran.r-project.org/web/packages/ggsci/vignettes/ggsci.html)或者[ggthemes](https://yutannihilation.github.io/allYourFigureAreBelongToUs/ggthemes/)选择合适的调色板
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/ggthemes-1.png/pic)

### 调色板hex代码查看
- [这里](https://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3)提供了`ColorBrewer`调色板动态预览及hex代码查看。
- 获取其他调色板hex
``` r
library(paletteer)
paletteer_c(package = "scico", palette = "berlin", n = 10)
```
- ggplot2 默认调色板查看
``` r
library(scales) 
# n为色板阶数
show_col(hue_pal()(n))
```

### 自定义调色板
当以上色板无法满足需求，可以通过以下方式自定义色板[^3]。

- 生成色板
主要使用`colorRampPalette`，详见[文档](https://www.rdocumentation.org/packages/dichromat/versions/1.1/topics/colorRampPalette)
``` r
# 定义基准色
cols<-c('#E64E00','#65B48E','#E6EB00','#E64E00')
# 生成色板 20 色阶
# bias用于设置色阶偏移 Bias >1 puts more colors at high values
pal<-colorRampPalette(cols, bias = 0.5)
image(x=1:20,y=1,z=as.matrix(1:20),col=pal(20))
```
- 查看色板
``` r
pal(20)

 [1] "#3E5CC5" "#4469BC" "#4A77B3" "#5085AA" "#5693A2" "#5CA199" "#62AF90" "#72B97F" "#86C268"
[10] "#9BCB52" "#AFD33B" "#C4DC25" "#D8E50E" "#E6E200" "#E6C900" "#E6B100" "#E69800" "#E67F00"
[19] "#E66600" "#E64E00"
```
- 使用色板
若使用时n小于设置阶数，则从左开始截断使用。
``` r
# 连续型
p + scale_color_gradientn(colors = pal(20))
# 离散型
p + scale_color_manual(colors = pal(n))
```

## Reference
[^1]: [Dealing with colors in ggplot2](https://www.r-graph-gallery.com/ggplot2-color.html)
[^2]: [ggplot2 colors : How to change colors automatically and manually?](http://www.sthda.com/english/wiki/ggplot2-colors-how-to-change-colors-automatically-and-manually)
[^3]: [R语言作图：自定义调色盘](https://zhuanlan.zhihu.com/p/144764468)