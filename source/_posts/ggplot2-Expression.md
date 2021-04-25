---
title: ggplot2 使用expression添加公式与数学符号
layout: post
author: MattZou
updated: 2020-07-16
date: 2020-07-16 10:38:00
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/plotmath 2-1.png/bg
tags:
- R
- ggplot2
description: 主要介绍ggplot2里添加表达式或符号
---

有时需要在图上标注诸如数学公式、上下标等符号，该操作可以通过`expression`函数完成。
## 原理
如果在ggplot2中`text`参数是一个表达式形式（`expression`），则该参数将被根据类似TeX的规则对进行格式化输出。`expression`可用于标题，注释以及x轴和y轴标签[^1]。

`expression`代码与输出的对应关系如图
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/expression.jpg/pic)

## 使用方式
### 普通label
如果只是输出单独符号，则独立使用**expression**即可，如果组合复杂公式或添加文本描述，则在**expression**中使用**paste**进行拼接。
``` r
# 全公式
 labels = expression(-pi, -pi/2, 0, pi/2, pi)
# 文本与符号组合
 xlab = expression(paste("Phase Angle ", phi))
```

### `facet_grid()`中的label
需要将label解析方式设置为"label_parsed"[^2]。
``` r
facet_grid(~ x, label = "label_parsed")
```

### `annotate()`中的label
**text**类型的标注，需将parse设置为TRUE。
``` r
annotate("text", label = expression(...), parse = TRUE)
```

## Reference
[^1]: [Mathematical Annotation in R](https://stat.ethz.ch/R-manual/R-devel/library/grDevices/html/plotmath.html)
[^2]: [Formatting Math Symbols and Expressions in ggplot Labels](https://www.benjaminackerman.com/post/2019-03-08-equation_labels/)