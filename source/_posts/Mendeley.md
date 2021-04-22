---
layout: post
title: Mendeley 文献引用格式设置
author: MattZou
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/Mendeley/group_7.svg
banner_img: https://cms-cdn.mendeley.com/prod/styles/1400_x_600/s3fs/C-5_ResearchManager@2x.png
date: 2021/04/03
updated: 2021/04/03
categories: 软件使用
tags: Mendeley
description:
---

> 最近使用Mendeley，研究了一下引文格式调整方法和技巧。
<!-- more -->

## Mendeley文献引用格式设置
**注意：仅适用于Windows环境，当前测试环境：系统版本Win10 21313.1000，软件版本1.19.8**
Mendeley引文格式主要通过在线方式下载更新，对格式的自定义可通过其提供的CSL在线编辑器进行，同时Mendeley也支持本地编译好的CSL文件配置。

### 引文格式配置文件  
[Citation Style Language](http://citationstyles.org/)
CSL用于配置引文格式，Zotero、Mendeley等软件支持此方式定义引文格式。

### Mendeley中设置常用期刊引文方式
1. 点击菜单栏`View`-`Citation Style`-`More Styles...`
2. 弹出窗口中选择`Get More Styles`
![安装](https://mattblog.oss-cn-beijing.aliyuncs.com/img/Mendeley/Mendeley_install.jpg/pic)
1. 搜索对应期刊下载安装即可

### Mendeley中编辑引文格式
1. 在弹出窗口中查看已安装引文格式，右键`Edit Style`，跳转进入浏览器进行在线编辑
![编辑](https://mattblog.oss-cn-beijing.aliyuncs.com/img/Mendeley/Mendeley\_edit.jpg/pic)
2. 可视化编辑区域
![在线编辑](https://mattblog.oss-cn-beijing.aliyuncs.com/img/Mendeley/Mendeley_online_edit.jpg/pic)
- 左侧树结构，可分别对全局设置、文内引用、参考书目、引文组件进行设置
- 鼠标悬停于右侧示例上，左侧树会高亮显这块引文内容涉及的配置项目
- **STYLE INFO**中`Global Formatting Options`，`default-locale`项目可调整切换‘等‘与’*et al*‘，参数分别为’zh-CN‘、’en-US‘
- 文内引文(**Inline Citations**)和参考书目(**Bibliography**)
	- 由`Group`结构构成，`Group`内成员由`Macros`定义
	- 点击根节点，其中`et-al-min`项目设置几个作者后出现’等‘
	- `Sort`节点，定义文献排列顺序，按作者、时间等升降序规则，可定义多个规则，优先级由上至下

### 本地CSL文件配置及使用
- 如果要使用[GB-T-7714] 相关CSL，可从以下repo下载[Chinese-STD-GB-T-7714-related-csl](https://github.com/redleafnew/Chinese-STD-GB-T-7714-related-csl)
- Mendeley将所有CSL文件存储在如下路径
>C:\Users\\{User}\AppData\Local\Mendeley Ltd\Mendeley Desktop\citationStyles-1.0
- 将下载后的CSL移动到此目录
- CSL文件类似XML样式，可以直接更改其中配置
![本地配置](https://mattblog.oss-cn-beijing.aliyuncs.com/img/Mendeley/Mendeley\_CSL.jpg/pic)
- 文件修改保存后，在`More Styles...`窗口中已安装可见。

## 推荐插件
### [Mendeley Reference Manager](https://www.mendeley.com/reference-management/reference-manager)
![](https://cms-cdn.mendeley.com/prod/2021-03/stable_release_desktop_latest_mrm_img.png?qQFcGBSStKhraR6U0otl7yyd3t0ZQdBR)
Mendeley现在主推**Mendeley Reference Manager**，貌似有意取代现有**Desktop**软件，感觉就是一个套壳浏览器😂

### [Mendeley Web Importer](https://www.mendeley.com/reference-management/web-importer)
![](https://cms-cdn.mendeley.com/prod/2019-09/web_importer_chrome_desktop.png?TOU3kk7tQZboiFYrIn90RYh1TyDQvaTg)
文献导入利器，浏览器随看随导，自动识别内容o(￣▽￣)ｄ