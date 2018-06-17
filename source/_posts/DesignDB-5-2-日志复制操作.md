---
title: DesignDB-5-2_日志复制操作
date: 2018-06-17 14:53:22
categories: mysql
tags:
    - mysql
    - designDB
    - 二进制复制
---

### 基于日志点的复制

**基于日志点复制的优点**

- 是MySQL最早支持的复制技术,BUG最少
- 对SQL查询没有限制
- 故障处理比较容易

**基于日志点复制的缺点**

- 故障转移时重新获取主的日志点信息比较困难

1. 在主DB服务器上建立复制账号

```sql
# ip段为从服务器日志网段
CREATE USER 'repl'@'IP段' identified by 'password'
```
进行授权操作
```sql
GRANT REPLICATION SLAVE ON *.* to 'repl'@'IP段';
```
2. 配置主数据库服务器

```
bin_log = mysql-bin  # 启用mysql日志并命名mysql-bin
server_id = 100      # 需要唯一
```

3.配置从数据库服务器
```
bin_log = mysql-bin
server_id = 101
relay_log = mysql-relay-bin # 这里建议和主日志名字配套,防止后期找不到

log_salve_upate = on [可选] # 把主服务器的日志写入从服务器日志 
read_only = on [on] # 可以阻止非管理权限对数据库进行写操作,建议启用
```

4. 初始化从服务器数据

```sql
mysqldump --master-data=2 -single-transation
```
或者
```sql
xtrabackup --slave-info
```
> 建议用xtrabackup,不会造成网络阻塞,存储引擎建议只使用innoDB

5. 启用复制连路

```sql
CHANGE MASTER TO MASTER_HOST='master_host_ip',
       MASTER_USER='repl',
       MASTER_PASSWORD='password',
       MASTER_LOG_FILE='mysql_log_file_name', # 主库的二进制文件名
       MASTER_LOG_POS=4;  # 二进制文件的偏移量
````
### 模拟操作

主服务器操作

SQL配置
```
mysql> create user repl@'192.168.3.%' identified by '123456';
mysql> grant replication slave on *.* to repl@'192.168.3.%';
```
my.conf

```
#Replice#`
server-id=1

# BINARY LOGGING#
log_bin = /home/mysql/sql_log/mysql-bin
```

从服务器配置

```
#Replice#`
server-id=2
relay_log = /home/mysql/sql_log/mysqld-relay-bin

# BINARY LOGGING#
log_bin = /home/mysql/sql_log/mysql-bin
```
主服务器进行备份发送操作

```
# 备份导出
mysqldump --single-transaction --master-data --triggers --routines --all-databases -uroot -p >>all.sql

# 发送操作
scp all.sql root@192.168.3.101:/root
```
从服务器导入备份

```bash
mysql -uroot -p < all.sql
```
从服务器开始主从
```sql
# master_log_file从all.sql有个CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.00003',MASTER_LOG_POS=1839

mysql> change master to master host='192.168.3.100',
       master_user='repl',
       master_password='123456',
       master_log_file='mysql-bin.00003',
       master_log_pos=1839;
```
# 启动
```
start slave
```
# 查看效果
```
mysql> show slaves status\G; # 从服务器上操作
mysql> show processlist \G; #主服务器上操作
```

### 基于GTID的复制


定义:

全局事务ID,为保证每个在主上提交的事务在复制集群中可以生成唯一的ID

GTID=source_id:transaction_id

步骤原理
1. 从服务器告诉主服务器已经执行的GTID值
2. 主服务器发送给从服务器未执行的GTID值
3. 从服务器执行未执行的GTID值

优点:
- 可以很方便的进行故障转移
- 从库不会丢失主库上的任何修改

缺点:
- 故障处理比较复杂
- 对执行的SQL有一定的限制


#### 步骤:

1. 在主DB服务器上建立复制账号:
```sql
mysql> create user repl@'192.168.3.%' identified by '123456';
mysql> grant replication slave on *.* to repl@'192.168.3.%';
```
2. 主库中的配置
```
bin_log=/usr/local/mysql/log/mysql-bin
server_id=100 # 要求唯一
gtid_mode=on
enforce-gtid-consiste #强制gtid一致性,会让下面这2种语句报错
    - create table ... select
    - 在事务中使用create temporary table建立临时表
    - 使用关联更新事务表和非事务表
log-slave-updates=on # mysql5.6和之前版本要启动此参数,5.7以后可略
```
3.从库的配置
```conf
server_id = 101
relay_log = /usr/local/mysql/log/relay_log
gtid_mode=on
enforce-gtid-consistency

log-slave-updates=on
read_only=on [建议]
# 下面2个是从服务器连接主服务器的信和中继日志连接的相关方式,默认存在文件中
master_info_repository=TABLE[建议]
master_info_repository=TABLE[建议]
```
4. 从服务器的初始化操作

mysqldump --master-data=2 -single-transaction
xtarbackup --slave-info

5.从服务器启动基于GTID的复制 
```sql
mysql> change master to master host='192.168.3.100',
       master_user='repl',
       master_password='123456',
       master_auto_position=1
```

### GTID演示过程

1. 建立复制账号
```sql
mysql> create user repl@'192.168.3.%' identified by '123456';
mysql> grant replication slave on *.* to repl@'192.168.3.%';
select user,host from user;  # 查看账号
show grants from repl@'192.168.3.%'; # 查看授权
```
2. 主服务器进行配置

```
# vim /etc/my.conf
gtid_mode=on
enforce_gtid_consistency=on
#log_slave_updates=on #5.7可以不用开启
```

3. 从服务器的配置
```
# vim /etc/my.conf
gtid_mode=on
enforce_gtid_consistency=on
master_info_repository=TABLE[建议]
master_info_repository=TABLE[建议]
#log_slave_updates=on #5.7可以不用开启
```
重启MySQL服务器,让参数生效
```
/etc/init.d/mysqld restart
```
4.初始化主服务器

```bash
# 初始化导出
mysqldump --single-transaction --master-data=2 --triggers --routines --all-databases -uroot -p >all2.sql 

# 拷贝到从库
scp -p9880 all2.sql root@192.168.3.101:/root
```
5. 从库上恢复
```bash
mysql -uroot -p < all2.sql
```
开始配置
```sql
mysql> change master to host='192.168.3.100',
       master_user='repl',
       master_password='123456',
       master_auto_position=1;
# 启动主从
mysql> start slave;
mysql> show slave status\G;
```
6. 在主库进行修改测试是否传递到从库

```sql
mysql> insert into t values (4,'dddd') ;
```

### 复制模式版本的选择

- 使用的MySQl版本
- 复制架构及主从切换的方式
- 所使用的高可用管理组件
- 对应用的支持程度

