---
title: ggplot2 添加拟合线及公式
layout: post
author: MattZou
updated: 2020-08-31
date: 2019-04-21 23:17:39
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/README-readme-04-1.png/bg
categories: 软件使用
tags:
- R
- ggplot2
description: 主要介绍ggplot2里拟合线与公式设置
---

主要介绍ggplot2里拟合线与公式设置

## 使用ggpmisc()
stat_smooth + stat_poly_eq组合实现拟合曲线以及拟合公式显示。

stat_poly_eq用于显示公式，支持以下统计参数显示[^1]：
> eq.label
equation for the fitted polynomial as a character string to be parsed

>rr.label
R2 of the fitted model as a character string to be parsed

>adj.rr.label
Adjusted R2 of the fitted model as a character string to be parsed

>f.value.label
F value and degrees of freedom for the fitted model as a whole.

>p.value..label
P-value for the F-value above.

>AIC.label
AIC for the fitted model.

>BIC.label
BIC for the fitted model.

### 使用
``` r
# 定义公式类型
formula <- y ~ x + I(x^2)
# 添加拟合线
P + geom_smooth(method = "lm", formula = formula)
# 添加公式
p + stat_poly_eq(aes(label =  paste(stat(eq.label), stat(adj.rr.label), 
                sep = "*\", \"*")), formula = formula, parse = TRUE)
# 添加参数表
p + stat_fit_tb(method = "lm",
              method.args = list(formula = formula),
              tb.type = "fit.anova",
              tb.vars = c(Effect = "term", 
                          "df",
                          "M.S." = "meansq", 
                          "italic(F)" = "statistic", 
                          "italic(P)" = "p.value"),
              label.y = 0.87, label.x = 0.1,
              size = 4,
              parse = TRUE
  )
```

stat_poly_eq显示p-value`stat(p.value.label)`，p显示为大写[^2]，如果需要改为小写斜体[^3]
``` r
# 将stat(p.value.label) 替换为
paste("italic(p)", substring(p.value.label,10))
```

### 对数指数形式
`stat_poly_eq`在显示指数或对数函数时总出现问题，因此考虑采用geom_line + geom_ribbon + annotate方式迂回实现[^4]。
``` r
# 公式拟合
log.model <-lm(log(Frequency) ~ Coverage, area)
# 用于geom_ribbon显示CI
observeddata <- cbind(area, predict(log.model, newdata = area, interval = 'confidence'))
# 组合生成拟合线与公式
p + geom_ribbon(data = observeddata, 
                aes(x = Coverage, y = exp(fit), ymin = exp(lwr), ymax = exp(upr)), ...) + 
  geom_line(data = log.model.df, aes(Coverage, y, color = 'b'), ...) + 
  annotate("text", ...)
```

## 使用ggPredict()
`ggPredict`提供了拟合曲线的封装函数，相比ggplot2原生绘制，更为简单，同时支持lm()，glm()等拟合方式[^5]。
### 安装
``` r
install.packages("devtools")
devtools::install_github("cardiomoon/ggiraphExtra")
```
### 对比原生方法
``` r
# 载入样例数据
require(moonBook)
# 拟合 
fit = lm(NTAV~age, data = radial)
# 使用ggplot的 geom_smooth 绘制
ggplot(radial, aes(y = NTAV,x = age)) + geom_point() + geom_smooth(method = "lm")
# 使用ggPredict绘制
ggPredict(fit, se = TRUE, interactive = TRUE)
```


## Reference
[^1]: [Equation, p-value, R^2, AIC or BIC of fitted polynomial](https://docs.r4photobiology.info/ggpmisc/reference/stat_poly_eq.html)
[^2]: [“ p值”的正确拼写（大写，斜体，连字符）吗？](https://qastack.cn/stats/871/correct-spelling-capitalization-italicization-hyphenation-of-p-value)
[^3]: [Can we neatly align the regression equation and R2 and p value?](https://stackoverflow.com/questions/61266084/can-we-neatly-align-the-regression-equation-and-r2-and-p-value)
[^4]: [How to visulaize linear model prediction in ggplot along with confidence interval?](https://stackoverflow.com/questions/41205153/how-to-visulaize-linear-model-prediction-in-ggplot-along-with-confidence-interva)
[^5]: [ggPredict-Visualize multiple regression model](https://cran.r-project.org/web/packages/ggiraphExtra/vignettes/ggPredict.html)