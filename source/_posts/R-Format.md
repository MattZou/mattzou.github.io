---
title: R 数据转换
layout: post
author: MattZou
updated: 
date: 2018-12-11
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/rc30v1-database-product-listing-all.png/bg
categories: R
tags:
- R
description: R中各类数据转换速查
---
只提供函数与功能关键字速查，具体用法可自己搜索官方文档，示例比较详尽。

## 字符串
``` r
# 获取字符串长度
nchar()

# 字符串分割
strsplit()

# 字符串拼接
paste(, sep="")

# 字符串截取
substr()

# 字符串替代
gsub()
chartr()
sub()

# 字符串匹配
grep()

# 大小写替换
toupper()
tolower()

```

## 数字
``` r
# 取整
## 向下
floor(...)
## 向上
ceiling(...)
## 四舍五入
round(...)

# 有效数字
## 小数点后开始
round(...)
## 从头开始
signif(...)

# 科学计数法
format(, scientific = T)

# 补零补位
sprintf('%03d', 1)

# 千分位间隔
prettyNum(, big.mark = ',')
```

## 时间
POSIXlt
``` r
> today<-Sys.time()
> unclass(as.POSIXlt(today))
$sec
[1] 53.27151
$min
[1] 38
$hour
[1] 20
$mday
[1] 6
$mon
[1] 5
$year
[1] 116
$wday
[1] 1
$yday
[1] 157
$isdst
[1] 0
$zone
[1] "CST"
$gmtoff
[1] 28800
attr(,"tzone")
```

## 正则表达式
R 字符串处理支持在`pattern`使用正则表达式[^1]
```
   '\n'          newline                                            
   '\r'          carriage return                                    
   '\t'          tab                                                
   '\b'          backspace                                          
   '\a'          alert (bell)                                       
   '\f'          form feed                                          
   '\v'          vertical tab                                       
   '\\'          backslash '\'                                      
   '\''          ASCII apostrophe '''                               
   '\"'          ASCII quotation mark '"'                           
   '\`'          ASCII grave accent (backtick) '`'                  
   '\nnn'        character with given octal code (1, 2 or 3 digits) 
   '\xnn'        character with given hex code (1 or 2 hex digits)  
   '\unnnn'      Unicode character with given code (1-4 hex digits) 
   '\Unnnnnnnn'  Unicode character with given code (1-8 hex digits) 

    [:alnum:]
    Alphanumeric characters: [:alpha:] and [:digit:].

    [:alpha:]
    Alphabetic characters: [:lower:] and [:upper:].

    [:blank:]
    Blank characters: space and tab, and possibly other locale-dependent characters such as non-breaking space.

    [:cntrl:]
    Control characters. In ASCII, these characters have octal codes 000 through 037, and 177 (DEL). In another character set, these are the equivalent characters, if any.

    [:digit:]
    Digits: 0 1 2 3 4 5 6 7 8 9.

    [:graph:]
    Graphical characters: [:alnum:] and [:punct:].

    [:lower:]
    Lower-case letters in the current locale.

    [:print:]
    Printable characters: [:alnum:], [:punct:] and space.

    [:punct:]
    Punctuation characters:
    ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~.

    [:space:]
    Space characters: tab, newline, vertical tab, form feed, carriage return, space and possibly other locale-dependent characters.

    [:upper:]
    Upper-case letters in the current locale.

    [:xdigit:]
    Hexadecimal digits:
    0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f.
```

详见[^2]

## Reference
[^1]: [Regular Expressions as used in R](https://stat.ethz.ch/R-manual/R-devel/library/base/html/regex.html)
[^2]: [R 使用的正则表达式](https://likan.info/cn/post/regular-expressions-used-in-r/)