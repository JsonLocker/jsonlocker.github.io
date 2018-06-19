---
title: DesignDB-3-2_mysqlslap.md
date: 2018-06-15 17:09:37
categories: mysql
tags:
    - designdb
    - mysql
    - 基准测试
    - mysqlslap
---

## MySQL 基准测试工具

### mysqlslap
- 默认mysql自带基准测试工具，
- 可模拟服务器负载,输出相关统计信息
- 可指定也可自动生成查询语句

### 常用参数

- --auto-generate-sql 由系统自动生成SQL脚本进行测试
- --auto-generate-sql-add-autoincrement 在生成的表中增加增ID
- --auto-generate-sql-load-type 指定测试中使用的查询类型
- --auto-generate-sql-write-number 指定初始化数据时生成的数据量,默认是100行
- --concurrency 指定并发线程的数量
- --engine 指定测试表的存储引擎,可用逗号分割多个存储引擎,和no-drop互斥
- --no-drop 完成1次测试后不清理测试数据
- --iterations 指定测试运行次数,和no-drop互斥
- --number-of-queries 指定每个线程执行的查询数量
- --debug-info 指定输出额外的内存和CPU统计信息
- --number-int-cols 指定测试表中包含int类型列的数量
- --number-char-cols 指定测试表中包含varchar类型列的数量
- --create-schema 指定用于执行测试的数据库名字
- --query 用于指定自定义SQL的脚本
- --only-print 并不运行测试脚本,而是把生成脚本打印出来

指定并发数量1 50 100 200的测试,
完成3次，
生成int数量为5行,
生成varchar类5行，
指定自动生成sql增加自增id
同时对2种表引擎进行测试
每个进程查询量为10次
在sbtest下进行执行

```bash
mysqlslap --concurrency=1,50,100,200 --iteration==3 --number-int-cols=5 --number-char--cols=5 --auto-generate-sql --auto-generate-sql-add-autoincrement --engine=myisam,innodb --number-of-queries=10 --create-schema=sbtest
```


