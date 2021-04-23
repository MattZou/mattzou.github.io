---
layout: post
title: Oracle impdp与expdp进行数据资产快速迁移
date: 2016-08-14 20:57:00
updated: 2021-04-21
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/rc32-pmr-sup-trusted.png/bg
author: MattZou
categories: 软件使用
tags: 
- Oracle
- Oracle Utilities
description: Oracle数据迁移工具
---
## 背景

对于Oracle数据迁移任务，官方提供了一系列工具[^1]，应用较多的是**Data Pump**工具和[**SQL*Loader工具**](https://mattzou.com/2016/03/18/Oracle_SQLLDR/)。

**Data Pump**工具主要适用于Oracle数据库之间数据迁移，**SQL*Loader工具**偏向于外部数据文件导入。
> - Oracle Data Pump technology enables very high-speed movement of data and metadata from one database to another.
> - SQL*Loader loads data from external files into Oracle Database tables.
> © Oracle 说的😎

**Data Pump**工具由以下三个模块组成：
> 1. Command-line clients, `expdp` and `impdp`
> 2. DBMS_DATAPUMP PL/SQL package (also known as the Data Pump API)
> 3. DBMS_METADATA PL/SQL package (also known as the Metadata API)

本文主要介绍`expdp` 和 `impdp` 的使用方法。

## 基本用法
环境： Windows 10 or latest， Oracle 11g or latest version
### 目录创建与授权
**Data Pump**工具导入导出的文件需要储存在实体目录下，并在Oracle中创建虚拟目录名称与之映射。

#### 目录查询
以管理员账号登陆**sqlplus**
``` sql
SQL>conn system/manger@orcl as sysdba
SQL>select * from dba_directories;
```
可以查看当前存在的目录，结果如下：
``` sql
OWNER                          DIRECTORY_NAME
------------------------------ -----------------------------
DIRECTORY_PATH
------------------------------------------------------------
SYS                            XMLDIR
c:\ade\aime_dadvfh0169\oracle/rdbms/xml
```
`DIRECTORY_PATH`是实体目录位置，请确保已经创建此目录
`DIRECTORY_NAME`是虚拟目录名称

#### 目录创建
可以使用已经存在的目录，或者创建新的目录(推荐)
```sql
SQL>create directory dump_dir as 'd:\test\dump';
```
`dump_dir`自定义名称，对应`DIRECTORY_NAME`
创建后可以再次[查询](#目录查询)是否创建成功。

#### 目录授权
给数据恢复或导出用户授权[^2]
```sql
SQL>grant read,write on directory dump_dir to scott;
```

### 导入与导出

#### expdp导出
导出生成的dmp会保存在上面创建的实体目录中。

1. 导出用户
```sql
expdp scott/tiger@orcl schemas=scott dumpfile=expdp.dmp directory=dump_dir;
```

2. 导出表
```sql
expdp scott/tiger@orcl tables=emp,dept dumpfile=expdp.dmp directory=dump_dir;
```

3. 按查询条件导
```sql
expdp scott/tiger@orcl directory=dump_dir dumpfile=expdp.dmp tables=emp query='where deptno=20';
```

4. 按表空间导
```sql
expdp system/manager@orcl directory=dump_dir dumpfile=tablespace.dmp tablespaces=temp,example;
```

5. 导整个数据库
```sql
expdp system/manager@orcl directory=dump_dir dumpfile=full.dmp full=y;
```

#### impdp导入
导入之前，需要确保[目录创建与授权](#目录创建与授权)完成，并且dmp文件已经拷贝至对应目录下。

1. 导入用户（从用户scott导入到用户scott）
```sql
impdp scott/tiger@orcl directory=dump_dir dumpfile=expdp.dmp schemas=scott;
```

2. 导入表（从scott用户中把表dept和emp导入到system用户中）
```sql
impdp system/manager@orcl directory=dump_dir dumpfile=expdp.dmp tables=scott.dept,scott.emp remap_schema=scott:system;
```

3. 导入表空间
```sql
impdp system/manager@orcl directory=dump_dir dumpfile=tablespace.dmp tablespaces=example;
```

4. 导入数据库
```sql
impdb system/manager@orcl directory=dump_dir dumpfile=full.dmp full=y;
```

5. 追加数据
```sql
impdp system/manager@orcl directory=dump_dir dumpfile=expdp.dmp schemas=system table_exists_action
```

## 注意事项
1. expdp和impdp是服务端工具程序，只能在oracle服务端使用，不能在客户端使用[^3]。
2. imp/exp命令与impdp/expdp工具产生的文件不通用，请勿混淆[^4]。



## Reference
[^1]: [Oralce Utilities](https://docs.oracle.com/en/database/oracle/oracle-database/21/sutil/)
[^2]: [Required Roles for Oracle Data Pump Export and Import Operations](https://docs.oracle.com/en/database/oracle/oracle-database/21/sutil/oracle-data-pump-overview.html#GUID-8B6975D3-3BEC-4584-B416-280125EEC57E)
[^3]: [ORACLE使用EXPDP和IMPDP数据泵进行导出导入的方法](http://blog.sina.com.cn/s/blog_67d41beb0100ixnb.html)
[^4]: [Oracle Data Pump (expdp, impdp) in Oracle Database 10g, 11g, 12c, 18c, 19c](https://oracle-base.com/articles/10g/oracle-data-pump-10g)