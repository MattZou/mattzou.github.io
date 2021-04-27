---
title: ggplot2 坐标轴调整
layout: post
author: MattZou
updated: 2020-08-31
date: 2019-11-23
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/coord_flip-1.png
categories: 软件使用
tags:
- R
- ggplot2
description: 主要介绍ggplot2里坐标轴调整
---
坐标轴是ggplot2 中调整较多的部分，本文从数据映射和样式两个方面介绍坐标轴常用设置。

## 数据映射
### 轴范围
对于数值形式的轴，最简单的设置方式是直接使用`lims()`类函数[^1]设置值域即可。
``` r
p + lims(...)
p + xlim(...)
p + ylim(...)
```
> 注意
> `lims`类操作对原始数据有操作，函数先根据范围过滤数据，再生成图。这对统计类图如**boxplot**造成很大影响，由于更改了数据，会使用错误统计结果绘图。多数情况下我们希望的范围限定是指图像裁切，即生成图后裁切多余的位置，因此对于统计类绘图使用`coord_cartesian`进行缩放更为安全[^2]。
``` r
p + coord_cartesian(...)
```

对于其他类型数据，在指定范围的同时，还需指定坐标间隔，使用以下系列函数
[scale_*_discrete](https://ggplot2.tidyverse.org/reference/scale_discrete.html)，[scale_*_continuous](https://ggplot2.tidyverse.org/reference/scale_continuous.html)，[scale_*_date](https://ggplot2.tidyverse.org/reference/scale_date.html)

``` r
# limits指定范围，breaks指定间隔，labels定义新的标记
# 离散型
p + scale_x_discrete(limits, labels), 
# 连续型
p + scale_x_continuous(limits, breaks), 
# 时间型
p + scale_x_date(limits, breaks, labels)
```

### 轴变换
- log变换
有三种方式实现对数坐标轴变换[^3]
``` r
# 1.aes或数据集里直接变换
ggplot(data, aes(x=, y=log10(y)))

# 2.scale_y_continuous 进行变换
p + scale_y_continuous(trans="log10",
        breaks=trans_breaks("log10",function(x)10^x),
        labels=trans_format("log10",math_format(10^.x)))

# 3.scale_y_log10 函数
p + scale_y_log10()
```

- 时间变换
使用`date_labels`进行时间转换[^4]
<table>
<thead><tr class="header">
<th align="left">String</th>
<th align="left">Meaning</th>
</tr></thead>
<tbody>
<tr class="odd">
<td align="left"><code>%S</code></td>
<td align="left">second (00-59)</td>
</tr>
<tr class="even">
<td align="left"><code>%M</code></td>
<td align="left">minute (00-59)</td>
</tr>
<tr class="odd">
<td align="left"><code>%l</code></td>
<td align="left">hour, in 12-hour clock (1-12)</td>
</tr>
<tr class="even">
<td align="left"><code>%I</code></td>
<td align="left">hour, in 12-hour clock (01-12)</td>
</tr>
<tr class="odd">
<td align="left"><code>%p</code></td>
<td align="left">am/pm</td>
</tr>
<tr class="even">
<td align="left"><code>%H</code></td>
<td align="left">hour, in 24-hour clock (00-23)</td>
</tr>
<tr class="odd">
<td align="left"><code>%a</code></td>
<td align="left">day of week, abbreviated (Mon-Sun)</td>
</tr>
<tr class="even">
<td align="left"><code>%A</code></td>
<td align="left">day of week, full (Monday-Sunday)</td>
</tr>
<tr class="odd">
<td align="left"><code>%e</code></td>
<td align="left">day of month (1-31)</td>
</tr>
<tr class="even">
<td align="left"><code>%d</code></td>
<td align="left">day of month (01-31)</td>
</tr>
<tr class="odd">
<td align="left"><code>%m</code></td>
<td align="left">month, numeric (01-12)</td>
</tr>
<tr class="even">
<td align="left"><code>%b</code></td>
<td align="left">month, abbreviated (Jan-Dec)</td>
</tr>
<tr class="odd">
<td align="left"><code>%B</code></td>
<td align="left">month, full (January-December)</td>
</tr>
<tr class="even">
<td align="left"><code>%y</code></td>
<td align="left">year, without century (00-99)</td>
</tr>
<tr class="odd">
<td align="left"><code>%Y</code></td>
<td align="left">year, with century (0000-9999)</td>
</tr>
</tbody>
</table>

``` r
p + scale_x_date(date_breaks = "5 years", date_labels = "%y")
```

### 轴形态
常用轴变换有以下三类
``` r
# x\y轴自身翻转
p + scale_*_reverse()

# x与y轴交换
p + coord_flip()

# 转换极坐标
p + coord_polar(...)
``` 

## 样式
轴样式的定义在`theme`中以**axis**开头[^5]
>`ticks`对应刻度线
>`text`对应标签
>`title`对应标题
>`line`对应轴线

![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/ggplot2/ggplot2-axis-ticks-example-1.png/pic)

### 刻度线
``` r
# 全局
  axis.ticks = element_line(...) 
# 只改变x轴     
  axis.ticks.x = element_line(...)
# 只改变y轴
  axis.ticks.y = element_line(...)
# 刻度线长度
  axis.ticks.length = unit(3, "pt")
```

### 轴标签
``` r
# 全局
  axis.text = element_text(...) 
# 只改变x轴    
  axis.text.x = element_text(...)
# 只改变顶端x轴（如果有）
  axis.text.x.top = element_text(...)
# 只改变y轴
  axis.text.y = element_text(...)
# 只改变右侧y轴（如果有）
  axis.text.y.right = element_text(...)
```

### 轴标题
- 定义标题样式
``` r
axis.title.x = element_text(...)
```
- 轴标题旋转
``` r
# angle定义旋转角度
# 由于旋转后文字会与坐标轴干涉冲突，可设置vjust或hjust偏移量调整
axis.title.y = element_text(angle = 0, vjust = 0.5)
```

### 轴线
设置轴线宽度
``` r
axis.line.y = element_line(size=0.25)
```

### 移除
如需移除相应内容
``` r
axis.* = element_blank()
```

## Reference
[^1]: [Set scale limits](https://ggplot2.tidyverse.org/reference/lims.html)
[^2]: [ggplot2中ylim的坑](https://www.jianshu.com/p/312d30049a25)
[^3]: [Position scales for continuous data (x & y)](https://ggplot2.tidyverse.org/reference/scale_continuous.html)
[^4]: [Date scale labels](https://ggplot2-book.org/scale-position.html#date-labels)
[^5]: [GGPLOT AXIS TICKS: SET AND ROTATE TEXT LABELS](https://www.datanovia.com/en/blog/ggplot-axis-ticks-set-and-rotate-text-labels/)

