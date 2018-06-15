---
title: DesignDB-3-3_sysbench
date: 2018-06-15 17:13:29
categories: mysql
tags:
    - designdb
    - sysbench
    - mysql
    - 基准测试
---

### MySQL基准测试工具 sysbench

#### 安装

```
wget https://github.com/akopytov/sysbench/archive/0.5.zip
unzip sysbench-0.5.zip
cd sysbench
./autogen.sh
./configure --with-mysql-includes=/usr/local/mysql/include/\ --with mysql-libs=/usr/local/mysql/lib
make && make install
```

#### 常用参数

>* --test 用于指定所要执行的测试类型,支持以下参数
    - Fileio 文件系统I/O性能测试
    - cpu cpu性能测试
    - memory 内存性能测试
    - Oltp 测试要指定具体的lua脚本
    - Lua脚本位于 sysbench-0.5/sysbench/tests/db
>* --mysql--db 指定执行基准测试的数据库名
>* --mysql--table-engine 指定所使用的存储引擎
>* --oltp--tables-count 执行测试的表数量
>* --oltp--tables-size 指定每个表中的数据行数
>* --num-threads 指定测试的并发线程数量
>* --max-time 指定最大的测试时间
>* --report-interval 指定间隔多长时间输出一次统计信息
>* --mysql-user 指定执行测试的MySQL用户
>* --mysql-password 指定执行测试用户的密码
>* prepare 用于准备测试数据
>* run 用于实际进行测试
>* cleanup 用于清理测试数据



