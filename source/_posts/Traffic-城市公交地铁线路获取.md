---
title: 城市公交地铁线路获取
layout: post
author: MattZou
updated: 
date: 2021-05-06 11:55:42
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/Traffic/%E5%8C%97%E4%BA%AC%E6%80%BB%E5%9B%BE.jpg/bg
categories: 软件使用
tags:
- 高德API
description: 基于高德API的城市公交系统基础数据获取
---

在城市交通研究中，公共交通系统是重要研究对象，其中公交地铁线路数据是研究的基础。由于公交线路会定期调整，因此通过服务商API动态获取线路信息是这类数据可靠的获取方式。

## 公交线路名称
### 思路
公交线路名称获取通过[8684网站](https://www.8684.cn/)查询解析获取[^1]，网站线路涵盖全，解析方便。

### 工具
解析工具采用`BeautifulSoup`，`BeautifulSoup`解析器区别如下[^2]：

| 解析器 	| 使用方法 	| 优势 	| 劣势 	|
|-	|-	|-	|-	|
| python标准库 	|BeautifulSoup(markup,"html.parser")| python内置标准库<br>执行速度适中<br>文档纠错能力强 	| python2.7.3以前的版本容错能力差 	|
| lxml HTML解析器 	|BeautifulSoup(markup,"lxml")| 速度快<br>文档纠错能力强 	| 需要安装C语言库 	|
| lxml xml解析器 	|BeautifulSoup(markup,["lxml","xml"])| 速度快<br>唯一的支持解析的xml的解析器 	| 需要安装C语言库 	|
| html5lib 	|BeautifulSoup(markup,"html5lib")| 最好的容错性<br>以浏览器的方式解析文档<br>生成html5格式的文档 	| 速度慢<br>不宜懒外部库 	|

这里解析器采用`lxml`，需先安装
``` 
    conda install lxml
```
### 代码
线路名称获取方式如下
``` python
# -*- coding: utf-8 -*-
# @Author  : MattZou
# @File    : BusLineName.py
# @Software: PyCharm
# -*-----------------*-

# Packages
import requests
import pandas as pd
from bs4 import BeautifulSoup

# 城市名称拼音
city_name = "zhengzhou"
# 初始化公交名称列表
name_list = []

# 获取列表
# 定义请求头信息
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36 Edg/90.0.818.51'}

# 查询城市网站地址
home_page = 'https://{}.8684.cn/'.format(city_name)
# 请求页面
home_html = requests.get(home_page, headers=headers)
# bs解析，使用lxml解析器
Soup = BeautifulSoup(home_html.text, 'lxml')
# 分类列表提取
list_div = Soup.find_all('div', class_='list')
# 以数字开头
num_div = list_div[0].find_all('a')
# 以汉字/字母开头
char_div = list_div[1].find_all('a')


# 列表解析
# 解析函数
def html_parse(div_list: list):
    parsed_list = []
    try:
        for a in div_list:
            # 链接提取
            href = a['href']
            html = home_page + href
            temp_html = requests.get(html, headers=headers)
            # 解析器
            Soup = BeautifulSoup(temp_html.text, 'lxml')
            # 下一级div
            cc = Soup.find('div', class_='cc-content').find_all('div')[-1]
            cc_a = cc.find_all('a')
            # 遍历添加
            for sub_a in cc_a:
                title = sub_a.get_text()
                parsed_list.append(title)
    except Exception as e:
        print('Network Error:', e)
    return parsed_list


# 以数字开头
name_list += html_parse(num_div)
# 以汉字/字母开头
name_list += html_parse(char_div)

# 输出文件为csv
name_pd = pd.DataFrame(name_list)
name_pd.to_csv('{}_bus_name.csv'.format(city_name), index=False, header=["Name"], encoding='utf8')
```
## 线路轨迹及站点
基于采集到的公交名称，应用高德API提取每路公交停站点及线路轨迹。


## 可视化

## Reference
[^1]: [Python：利用高德API获取公交路线并可视化](https://blog.csdn.net/sinat_36226553/article/details/104948734)
[^2]: [爬虫：python之BeautifulSoup(lxml)](https://blog.csdn.net/zhangzejia/article/details/79658221)
