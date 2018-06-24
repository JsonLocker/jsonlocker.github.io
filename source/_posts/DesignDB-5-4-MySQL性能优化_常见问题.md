---
title: DesignDB-5-4_MySQL多线程从库配置及常见问题
date: 2018-06-24 09:40:06
categories: mysql
tags:
    - mysql
    - designdb
---

### 影响主从延迟的因素

- 主库写入二进制日志的时间 
    - 控制主库的事务大小,分割大事务
- 二进制日志的传输时间
    - 使用混合格式MIXED日志格
    - 设置set binlog_row_image=minimal;
- 默认从库只有1个SQL线程,主上并发的修改在从上变成串行
    - 使用5.7版本以后的多线程复制

**配置多线程复制**

前提: 完成主从复制的配置
1. 在从服务器上停止链路复制  stop slave`
2. 设置链路并发为逻辑时钟的方式 `set global slave_parallel_type='logical_clock'; 
3. 设置复制线程数量,也就是并发线程数 `set global slave_parallel_workers=4`;
4. 启动复制 `start slave`

MySQL中进行从库并发配置操作:

```sql
# 从库中查看sql线程
> show processlist;
# 停止线程
> stop slave;
# 查看状态
> show slaves status\G;
# 查看现在并发配置,默认是database
> show variables like 'slave_parallel_type';
# 设置并发方式
> set global slave_parallel_type='logical_clock';
# 查看当前并发线程数量,默认是0
> show variables like 'salve_parallel_workers';
# 修改为4线程
> set global slave_parallel_workers=4;
# 启动复制
> start slave;

# 查看状态
> show slave status\G;
# 查看IO线程
> show processlist\G;
```

### 常见问题

1. 由于数据损坏或丢失引起的主从复制错误
    - 一般由于主库或从库意外down机引起
        1. 跳过二进制日志事件
        2. 注入空事务方式先恢复中断的复制链路
        3. 用其他方式来比对主从服务器的数据
    - 主库上的二进制日志损坏
        1. 在从库中用change_master命令来重新指定
        2. 对丢失对数据进行修复
        3. 对主从数据库的数据进行检验
    - 备库的中继日志的损坏
        1. 在从库中用change_master命令来重新在损坏位置进行读取主库二进制日志
    - 在从库进行了数据修改,造成主从复制错误
        1. 需要在从库上设置 `read_only`参数,**这个非常重要!!!!!!!**       
    - 存在不唯一的server_id或者server_uuid,
        - 多从服务器使用同一个server_uuid的问题
        - server_uuid是记录在数据目录中的auto.conf中,很难背发现
    - 从服务器上最大允许包`max_allow_packet`设置不一致,引发主从复制错误

### MySQL无法解决的问题

1. 无法分担主数据库的写负载
    - 通过分库分表来处理
2. 自动进行故障转移和主从切换
3. 提供读写分离的功能
