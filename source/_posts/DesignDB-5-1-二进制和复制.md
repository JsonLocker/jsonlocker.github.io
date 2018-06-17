---
title: DesignDB-5-1_二进制和复制
date: 2018-06-17 13:31:02
categories: mysql
tags:
    - designdb
    - mysql
    - mysql 复制
    - mysql 二进制
---

### MySQL的复制功能

优点
- 基于2进制日志增量进行
- 无需太多带宽
- 用复杂功能增加备库,分担读负载,实现数据读取负载均衡
- 高可用/灾难恢复/备份提供更多选择
- 实现数据库的在线升级

缺陷:
- 复制为异步,会造成不同步
- 无法保证主库和备库的延迟
- 复制不能代替备份

## MySQL的二进制日志

MySQL日志包括2种层级

1. MySQL服务层日志
    - 二进制日志
    - 慢查日志
    - 通用日志
2. MySQL存储引擎层日志
    - innodb的回滚日志
    - innodb的重做日志

### 二进制日志
    
记录了所有对MySQl数据库的修改事件,包括增删改查事件和对表结构的修改事件

注意: 
- 成功执行的才会被记录,没成功和执行失败的不会被记录
- 可用官方工具binglog对日志内容进行查看

二进制日志格式

#### 1. 基于段的格式 binlog_format=STATEMENT (可以清楚查看sql语句)
- 优点: 只记录slq语句,记录量相对较小,节约磁盘和网络I/O
- 缺点: 
    - 必须记录上下文信息,保证语句在从服务器上执行结果和主服务器一致
    - 对于特定函数UUID(),user()这样非确定性函数无法复制
```
# 查看格式
show variables like 'binlog_format';

# 修改成段格式
set session binlog format=statement

# 查看binlog日志
show binary logs;

# 刷新binlong
flush logs;
```
进行试验数据操作

```sql
create datebase crn;
create table t (id int,c1 varchar(10));
insert into t values (1,"aa"),(2,'bb');
update t set c1="dd" where id=1;
```
查看日志内容
```sql
# cd /home/mysql/sql_log
mysqlbinlog sql_log
```

#### 2. 基于行的格式 binlog_format=ROW (可以清楚查看sql语句)

- 可以避免MySQL复制中出现的主从不一致的问题
- 会记录具体修改数据.
    - 比如同1语句修改1W行SQL语句,ROW会记录1W个具体语句,段格式则只记录一条语句

优点:
- 使得MySQL主从复制更加安全
- 对每一行数据的修改比基于段的复制效率更高
- 会记录详细操作,让误操作的数据可以通过分析二进制日志,利用反向操作处理得到恢复。

缺点:
- 日志量很大
- 消耗更多的磁盘I/O 和网络I/O资源

控制ROW格式记录的方式
binlog_row_image = [FULL | MINIMAL | NOBLOB]
    - FULL(默认) 会记录所有
    - MINIMAL 只会修改一个列前后的变化
    - NOBLOB 对BLOB和TAXT列修改则不会记录相应的值

```sql
# 设置二进制格式
set session binlog_format = row;
# 查看并清除二进制格式
show variables like 'binlog_format';
flush logs;
# 查看image值
show varables like 'binlog_row_image';
```
查看日志

```msql
# cd /home/mysql/mysql/log

# 需要加入-vv来查看里面的内容,否则说乱码
mysqlbinlog -vv mysql-bin.000003 | more;
```


#### 3. 混合日志格式 binlog_format=MIXED

特点:
－由系统决定基于行日志格式还是段日志格式,系统一般会判断基于段日志格式
- 数量的大小由所执行的SQL语句决定

**选用二进制日子格式的建议**: 用ROW格式并搭配binlog_row_image=minimal

### 二进制日志格式对复制的影响
1. 基于SQL语名的复制(SBR)
    - 二进制日志使用statement格式
2. 基于行的复制(RBR)
    - 二进制日志使用的是基于行的日志格式
3. 混合复制
    - 会根据实际情况在SBR和RBR之间进行切换

#### SBR

优点:
- 日志量少,节约网络传输I/O
- 不强制主从数据库的表定义完全相同
- 相比基于行的复制方式更灵活

缺点:
- 对非确定性事件,无法保证主从复制数据的一致性
- 对于存储过程,触发器,自定义函数进行的修改也可能造成数据不一致
- 相比行复制,执行时需要更多的行锁

#### RBR

优点:
- 可应用于任何SQL的复制包括非确定性函数,存储过程。
- 可以减少数据库锁的使用

缺点:
- 要求主从数据库的表结构相同,否则可能会中断复制
- 无法在从上单独执行触发器

==总结:建议用RBR,数据更安全==

### MySQL复制的工作方式

1. 主服务器将变更写入二进制日志
2. 从服务器读取主的二进制日志变更并写入到relay_log中
    - 基于日志点的复制
    - 基于GTID的复制
3. 从服务器从relay_log上读取日志并在自身进行重放操作
    - 基于SQL段的日志是在从库上从新执行SQL语句
    - 基于行的日志则是在从库上之间应用对数据库的修改
