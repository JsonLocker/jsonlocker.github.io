---
title: DesignDB-2-01_服务器硬件
date: 2018-06-15 17:18:50
categories: mysql
tags:
    - designdb
    - 硬件影响
---


## 影响性能的几个方面

1. 服务器硬件
2. 服务器使用的操作系统
3. 数据库存储引擎的选择
    - MyISAM: 不支持事务，表级锁
    - InnoDB: 事务级存储引擎，完美支持行级锁，事务ACID特性.
4. 数据库参数配置
5. 数据库结构设计和SQL语句
    - 慢查询
    - 数据库表结构设计不合理
    - 查询语句不合理
