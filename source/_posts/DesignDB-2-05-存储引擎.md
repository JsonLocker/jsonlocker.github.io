---
title: DesignDB-2-05_存储引擎
date: 2018-06-15 17:26:52
categories: mysql
tags:
    - designdb
    - engine
---

### MyISAM 存储引擎
- 是mysql5.5之前的默认存储引擎。
- 文件: 由MYD和MYI 2种文件构成
- 特性: 
    - 并发性和锁级别 
    - 表损坏修复
        - check table tablename 检查表
        - repair table tablename 恢复表

    ```
    create table myisam ( id int, c1 varchar(20))engine=myisam charset=utf8;
    check table myisam;
    repair table myisam;
    ```
    - 支持全文索引
    - 支持数据压缩 (myisampack),压缩后只能进行读操作
    ```
    # myisampack -b -f myIsam.MYI
    #ls -lh  myisam*
    
    myIsam.MYI 压缩后新文件
    myIsam.OLD 原文件
    ```

   - 限制
    - 默认的版本< MYSQL5.0 的默认表大小4G
        - 修改MAX_Rows 和AVG_ROW_LENGTH 后可以存储大表
        - 大于MySQL5.0版本默认支持256TB，不用修正


    - 支持场景
        - 非事务型应用,一般不涉及财务的业务都可以支持.
        - 只读类应用,因为支持压缩后无法写入,报表类比较适合
        - 空间类应用.
    
### InnoDB 存储引擎
    
- 在MYSQL5.5后版本变成默认存储引擎
- InnoDB使用表空间进行数据存储
    - 配置参数 innodb_file_per_table 进行表存储空间配置
        - ON: 独立表空间, 存储tablename.ibd
        - OFF: 系统表空间,ibdataX

```
＃ 查看存储状态
show variables like 'innodb_file_per_table'

# 关闭当前状态
set global innodb_file_per_table = off;
```
==强烈建议用独立表空间进行存储===

把系统表空间转独立表空间步骤

1. mysqldump导出所有数据库表数据
2. 停止mysql服务,修改参数，并删除InnoDB相关文件
3. 重启MySQL服务,重建Innodb系统表空间
4. 重新导入数据

#### InnoDB 特性

1. 是一种事务性的存储引擎
2. 完成支持事务的ACID特性
3. Redo log(实现事务持久性) 和Undo Log(未提交事务回滚和多版本控制)
        - show variables like 'innodb_log_files_in_group';
        - show variables like 'innodb_log_buffer_size';
4. 区别于MyiSAm的表级锁,innodb属于行级所.可支持更大的并发
5. 行级锁是由存储引擎层实现的.


### 额外知识

### 锁
- 锁主要是管理共享资源的并发访问
- 用于实现事务的隔离性
- 锁类型
    - 共享锁(也称读锁)
    - 独占锁(也称写锁)
- 锁的粒度
    - 表级锁 

```
begin;
insert into into user value (2,'aaa'),(3,'bbb');

# 上面会锁定,实现行级锁,其他用户无法写入，直到执行完毕开始回滚
rollback;
```
    - 实现表级锁
```
lock table user write;

# 上面实现表级锁,其他链接用户无法读取直到下面解锁操作
unlock tables;
```


### 阻塞和死锁

- 阻塞是为了事务可以并发和正常运行。
- 死锁可以由系统自动处理

innodb 状态监测
```
show engine innodb status
```

#### 适用场景

一般建议都适用InnoDB，适用于大多数的OLTP应用。

MySQL5.7之后已经支持全文索引和空间函数！！

#### 如何选择存储引擎

==建议InnoDB,不要混合使用存储引擎==

- 事务
- 备份
- 崩溃恢复
- 存储引擎的特有特性



