---
title: ORA-xxxxx
layout: post
author: MattZou
updated: 2021-04-23 00:13:15
date: 2016-08-14 00:13:15
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/rc32-pmr-sup-comprehensive.png/bg
categories: 软件使用
tags:
- Oracle
description: 如何从坑里爬起😂 Oracle 错误代码及处理
---

## ORA-01659: 无法分配超出 7 的 MINEXTENTS
主要原因是表空间不够,将其设置为自动扩展即可。
``` sql
alter database datafile 'D:/app/Administrator/oradata/user_data.dbf' 
autoextend on;
```
或者可以在创建perfstat表空间的时候进行设置：
``` sql
create tablespace perfstat
datafile 'D:/app/Administrator/oradata/user_data.dbf' 
size 100m 
autoextend on
extend management local;
```
> 更多内容可见[Oracle 表空间管理](http://localhost:4758/2016/03/15/Oracle_%E8%A1%A8%E7%A9%BA%E9%97%B4/)

## ORA-32773: 不支持对小文件表空间 TEST 的操作
需要扩展表空间
1. 增加数据文件
2. 增加数据文件的大小
3. 设定数据文件自动增长

> 更多内容可见[Oracle 表空间管理](http://localhost:4758/2016/03/15/Oracle_%E8%A1%A8%E7%A9%BA%E9%97%B4/)

## ORA-01830: 日期格式图片在转换整个输入字符串之前结束
`to_date`比较的时候报这个错误
问题：**varchar2**类型转换成**date**类型
``` sql
select to_date(INVOICE_DATE,'yyyy-mm-dd') from tab;
```
最后查的原因：INVOICE_DATE=‘2005-11-10 00:00:00’的长度大于格式化'yyyy-mm-dd'的长度
解决：使用substr()
``` sql
to_date(substr(INVOICE_DATE,1,10),'yyyy-mm-dd')
``` 

## ORA-01810：格式代码出现两次
1. Oracle中使用to_date()时格式化日期需要注意格式码，如：
``` sql
select to_date('2005-01-01 13:14:20','yyyy-MM-dd HH24:mm:ss') from dual;
``` 
原因是SQL中不区分大小写，MM和mm被认为是相同的格式代码，所以Oracle的SQL采用了mi代替分钟。
``` sql
select to_date('2005-01-01 13:14:20','yyyy-MM-dd HH24:mi:ss') from dual;
```
2. 另要以24小时的形式显示出来要用HH24
``` sql
select to_char(sysdate,'yyyy-MM-dd HH24:mi:ss') from dual;//mi是分钟
```