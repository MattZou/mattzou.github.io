---
title: Oracle 表空间管理
layout: post
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/rc32-pmr-sup-secure.png/bg
author: MattZou
date: 2016-03-15 20:51:39
updated: 2021-04-22 14:58:39
categories: 软件使用
tags: 
- Oracle
- Tablespace
description: Oracle表空间相关操作
---
## 概念

表空间用于组织管理实体数据文件
> Oracle stores data logically in tablespaces and physically in datafiles associated with the corresponding tablespace.[^1]

![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/sql/Datafiles%20and%20Tablespaces.gif/bg)

自Oracle 10g后，Oracle提供两类表空间`BigFile Tablespace，BFT`和`SmallFile Tablespace，SFT`，分别支持不同类型的**datafiles**，关于这两类表空间详细内容，可以参考此文[^2]。本文主要以`SmallFile Tablespace`类型表空间实现为主，这也目前Oracle默认的表空间设置。

## 创建
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/sql/Enlarging%20a%20Database%20by%20Adding%20a%20New%20Tablespace.gif)
以表空间所属用户身份登录
``` sql
-- 表空间名 tb_name
create tablespace tb_name  
-- 开启log
logging  
-- 指定数据文件存放路径及文件名
datafile 'D:/app/Administrator/oradata/user_data.dbf'
-- 初始大小
size 50m  
-- 自动拓展 开
autoextend on  
-- 每次自动拓展大小
next 50m maxsize unlimited
extent management local;  
```

## 查询
查询已有表空间
``` sql
SELECT a.tablespace_name                        "表空间名",
       total                                    "表空间大小",
       free                                     "表空间剩余大小",
       ( total - free )                         "表空间使用大小",
       Round(( total - free ) / total, 4) * 100 "使用率   %"
FROM   (SELECT tablespace_name,
               Sum(bytes) free
        FROM   DBA_FREE_SPACE
        GROUP  BY tablespace_name) a,
       (SELECT tablespace_name,
               Sum(bytes) total
        FROM   DBA_DATA_FILES
        GROUP  BY tablespace_name) b
WHERE  a.tablespace_name = b.tablespace_name
```

## 扩展
根据官方文档说明，扩展表空间有两种方式
> - Add a datafile to a tablespace
> - Increase the size of a datafile

第一种用于扩展系统表空间或其他已经存在的表空间
第二种用于直接拓展**datafile**大小，如果创建时设置了自动拓展就无需设置了
{% gi 2 2%}
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/sql/Enlarging%20a%20Database%20by%20Adding%20a%20Datafile%20to%20a%20Tablespace.gif)
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/sql/Enlarging%20a%20Database%20by%20Dynamically%20Sizing%20Datafiles.gif)
{% endgi %}

第一种实现如下
``` sql
ALTER TABLESPACE tb_name 
-- 不要跟已有文件冲突
ADD DATAFILE 'D:/app/Administrator/oradata/user_data_1.dbf'
-- 初始大小
size 50m 
-- 自动拓展 开
autoextend on 
-- 每次自动拓展大小 可以设置大一些
next 1024m maxsize unlimited;
```

## 收缩
删除表或者删除较多内容后，**datafile**文件不会自动缩减，可以通过`resize`方式重置表空间大小。用下面代码可以生成不同**datafile**的`resize`方案，再分别执行。

``` sql
SELECT /*+ ordered use_hash(a,c) */   
    'alter database datafile '''
    || a.file_name
    || ''' resize '
    || round(a.filesize -(a.filesize - c.hwmsize - 100) * 0.8)
    || 'M;',
    a.filesize,
    c.hwmsize
FROM
    (
        SELECT
            file_id,
            file_name,
            round(bytes / 1024 / 1024) filesize
        FROM
            dba_data_files
    )  a,
    (
        SELECT
            file_id,
            round(MAX(block_id) * 8 / 1024) hwmsize
        FROM
            dba_extents
        GROUP BY
            file_id
    )  c
WHERE
        a.file_id = c.file_id
    AND a.filesize - c.hwmsize > 100
```
对于**TEMP**表空间，可以采用`shrink`方式[^3]，与之相关还有`move`操作，可阅读相关文章[^4]
``` sql
-- 查询temp表空间情况
 select * from dba_temp_free_space;
-- 收缩
  alter tablespace temp shrink space;
```


## Reference
[^1]: [Tablespaces, Datafiles, and Control Files](https://docs.oracle.com/cd/B19306_01/server.102/b14220/physical.htm)
[^2]: [BigFile和SmallFile表空间技术](https://www.modb.pro/db/7369)
[^3]: [ALTER TABLE ... SHRINK SPACE Command : Online Segment Shrink for Tables, LOBs and IOTs](https://oracle-base.com/articles/misc/alter-table-shrink-space-online)
[^4]: [Oracle move和shrink释放高水位空间](https://blog.51cto.com/fengfeng688/1955137)
