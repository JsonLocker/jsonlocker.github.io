---
title: DesignDB-2-06_服务器参数
date: 2018-06-15 17:27:50
categories: mysql
tags:
    - designdb
    - mysql
    - config
---

### MySQL服务器参数

MySQL获取配置信息路径

- 命令行参数
    - mysqld_safe --datadir=/data/sql_data
- 配置文件（长期用的配置建议写入这里）
    - 用命令查询配置文件
    - mysqld --help ---verbose | grep -A 1 'Default options' 
    - /etc/my.conf/mysql/my.conf/home/mysql/my.conf/~/.my.conf

- 参数作用域
    - 全局参数
        - mysql客户端执行: set global 参数名=参数值 或 set @@global.参数名:=参数值
    - 会话参数
        - mysql客户端执行: set [session] 参数名=参数值 或 set @@session.参数名:=参数值

```
# 一起更改才会生效,否则系统自动取最大值
show variables where variable_name='wait_timeout' or variable_name='interactive_timeout';
set golbal wait_time=3600;
set golbal interactive_timeout=3600;
```

### 内存参数配置

#### 需要搞清楚以下问题:

- 确认可以使用内存的上限
- 确认给每个连接使用的内存
```
sort_buffer_size        每个线程排序缓存区的大小
join_buffer_size        每个线程所使用的连接缓冲区的尺寸
read_buffer_size        需要是4k的倍数
read_rnd_buffer_size 
```

以上如果配置过大都可能造成mysql内存溢出导致服务器崩溃


#### 确定需要为操作系统保留多少内存
#### 为缓冲池分配内存
- innodb_buffer_pool_size
    - 配置方案: 总内存－(每个线程所需内存*连接数) - 系统保留内存
    - 手册建议: 服务器内存的75%以上
- key_buffer_size ( myisam 表所需缓存池)
    - 配置方案: select sum(index_length) from information_schema.tables where engine='myisam'

### I/O参数配置
原理是事务日志先记录在缓冲日志，然后再写进磁盘

- InnoDB_log_file_size 控制单个事务日志大小
- InnoDB_log_files_in_group 控制事务文件的个数
- 事务日志总大小= Innodb_log_file_size * InnoDB_log_files_in_group;
- Innodb_log_buffer_size 事务日志缓冲区大小,每秒都会刷新,一般保留1s的事务即可.

#### 建议InnoDB 优化配置如下
- InnoDB_flush_log_at_trx_commit 事务日志刷新频繁程度
    - 0 每秒进行1次log写入cache,并flush log到磁盘
    - 1[默认] 每次事务提交执行log写入cache,并flush log到磁盘
    - 2[推荐] 每次事务提交,执行log数据写入到cache,每秒执行一次flush log到磁盘

一般修改单个事务日志大小即可,数值为可记录事务1小时信息。

- innodb_flush_method=O_DIRECT  控制innodb的刷新方法
- innodb_file_per_table = 1 控制innodb如何使用表空间
- innodb_doublewrite = 1 控制innodb使用双写缓冲

#### 建议MyISAM 优化配置如下

- delay_key_write
    - OFF: 每次写操作后刷新键缓冲中的脏块到磁盘
    - ON: 只对在键表时指定了 delay_key_write选项的表使用延迟刷新
    - ALL: 对所有MYISAM表都使用延迟键写入

### 安全相关配置参数

- expire_logs_days 指定自动清理binlog的天数
- max_allowed_packet  mysql可接收的包的大小,默认偏小推荐32M
- skip_name_resolve 禁用DNS查找,强烈建议关闭它
- sysdate_is_new 确保sysdate()返回确定性日期,建议增加此配置确保主从复制正确性
- read_only 禁止非super权限用户的写权限,主从复制的从服务器建议开启此操作
- skip_slave_start 禁用slave自动恢复,用于主从复制的从库
- sql_mode 设置mysql使用的SQL模式,默认比较宽松,这个谨慎修改,可能造成现有程序无法运行
    - strict_trans_tables
    - no_engine_subtitution
    - no_zore_date
    - no_zore_in_date
    - only_full_group_by

### 常用的配置参数

- sync_binlog 控制MYSQL如何向磁盘刷新binlog,默认0不主动刷新,建议设置1
- tmp_table_size 和 max_heap_table_size 一起使用,控制内存临时表大小
- max_connections 控制允许的最大连接数,默认100太小,通常改成2000
