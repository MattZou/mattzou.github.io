---
title: R 连接Oracle数据库
author: MattZou
updated:
date: 2016-12-06
index_img: https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/AppDev_oracle-r_detailed.svg
categories: R
tags:
- R
- Oracle
description: R连接Oracle数据库方法及对比
---

在分析任务中，需要将Oracle中部分数据提取出来，在R中进行进一步分析处理。
R主要有三种方式连接操作Oracle：
- RODBC
- ROracle
- RJDBC
其中RJDBC在性能和可伸缩性上都具有明显的局限性，基本不再推荐使用。


## RODBC
RODBC是一个实现ODBC数据库连接的R包，通过ODBC方式及函数访问数据库，并将数据库表内容转化为R对象，支持sql操作，生成R对象为dataframe类型。

要保证RODBC使用，需要在系统中配置好ODBC连接，在Windows系统下配置方法
1. [下载](https://www.oracle.com/database/technologies/instant-client/winx64-64-downloads.html)驱动包，包括base包和ODBC包，分别`instantclient-basic-`和`instantclient-odbc-`开头，并保证版本一致。
2. 解压上面两个包，把解压后的文件放在同一个目录，运行**odbc_install.exe**安装
3. 在`ODBC数据源管理器`中进行`Oracle ODBC Driver Configuration`
更多内容参见[^1]

RODBC使用方式如下，对于数据导出任务，RODBC是很便捷的实现方式。
``` r
	library(RODBC)
    # 创建连接
	con <- odbcConnect("ora", uid="orcl", pwd="pw", rows_at_time = 500)
    # 写入表数据
	sqlSave(con, test_table, "TEST_TABLE")
    # 执行查询
	sqlQuery(con, "select count(*) from TEST_TABLE")
    # 输出表数据
	date <- sqlQuery(con, "select * from TEST_TABLE")
    # 关闭连接
    close(con)
```

## ROracle

ROracle基于高性能OCI库，实现R与Oracle高性能连接，并支持事务级别的控制和用户提供的SQL语句的执行[^2]。
ROracle依赖`data.table`包，需提前安装。

ROracle配置方式
1. 根据数据库版本，对应安装[Oracle Instant Client](www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-win64soft-094461.html)
2. 系统环境变量添加两个：OCI_INC以及OCI_LIB64，分别对应Client目录下`\oci\include`和`\BIN`
3. 安装ROracle包

ROracle 连接使用如下
``` r
    library(ROracle)
    library(DBI)
    drv <-dbDriver("Oracle")
    con.string <- '(DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 127.0.0.1)(PORT = 1521))(CONNECT_DATA =(SERVER = server)(SERVICE_NAME = orcl)))'        
    con <- dbConnect(drv, username = "user", password = "pw", dbname = con.string)
    rs <- dbSendQuery(con,"select * from TEST_TABLE")
    data <- fetch(rs)
    dbDisconnect(con)
```

## 性能
根据ROracle官方文档描述，其性能是最好的[^3]。更为详细的评测可见此blog[^4]
![](https://mattblog.oss-cn-beijing.aliyuncs.com/img/index_img/Roracle.jpg/pic)

总之，ROracle优势在于其对应R的对象为data.table，并且支持数据类型同步，避免了很多跨平台转化的问题，在大数据量的情况下推荐使用。RODBC在各种平台上配置可能会有许多困难，因此在有配置好ODBC环境下，小数据量数据传输操作可以使用RODBC。我许多计算都是在数据库完成，R和Oracle交互主要是实现可视化，数据量相对较小，因此使用RODBC。


## Reference
[^1]: [Windows Oracle ODBC安装配置](https://www.cnblogs.com/shelvenn/p/3799849.html)
[^2]: [ROracle is an Open Source R Package available on CRAN](https://www.oracle.com/database/technologies/appdev/roracle.html)
[^3]: [ROracle Package](https://www.oracle.com/technetwork/database/database-technologies/r/roracle/201403-roracle-2167267.pdf)
[^4]: [R to Oracle Database Connectivity: Use ROracle for both Performance and Scalability](https://blogs.oracle.com/r/r-to-oracle-database-connectivity%3a-use-roracle-for-both-performance-and-scalability)