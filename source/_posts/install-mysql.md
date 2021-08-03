---
title: install_mysql
date: 2018-06-14 13:51:29
category: linux
tags:
    - mysql
    - linux
    - lnmp
---

### 1、配置YUM源
在MySQL官网中下载YUM源rpm安装包：http://dev.mysql.com/downloads/repo/yum/

```
# 下载mysql源安装包
shell> wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
# 安装mysql源
shell> yum localinstall mysql57-community-release-el7-8.noarch.rpm
```
检查mysql源是否安装成功
```
shell> yum repolist enabled | grep "mysql.*-community.*"
```
所示表示安装成功
```
mysql-connectors-community/x86_64 MySQL Connectors Community                  51
mysql-tools-community/x86_64      MySQL Tools Community                       63
mysql80-community/x86_64          MySQL 8.0 Community Server                  17
```

### 2、安装MySQL

```
shell> yum install mysql-community-server
```

### 3、启动MySQL服务
```
shell> systemctl start mysqld
```
查看MySQL的启动状态
```
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2018-06-14 00:11:49 EDT; 5min ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 9020 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 9037 (mysqld)
   Status: "SERVER_OPERATING"
   CGroup: /system.slice/mysqld.service
           └─9037 /usr/sbin/mysqld

Jun 14 00:11:48 host.localdomain systemd[1]: Starting MySQL Server...
Jun 14 00:11:49 host.localdomain systemd[1]: Started MySQL Server.

```
### 4、开机启动

```
shell> systemctl enable mysqld
shell> systemctl daemon-reload
```
### 5、修改root默认密码

mysql安装完成之后，在/var/log/mysqld.log文件中给root生成了一个默认密码。通过下面的方式找到root默认密码，然后登录mysql进行修改：
```
shell> grep 'temporary password' /var/log/mysqld.log
shell> mysql -uroot -p
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
或者

mysql> set password for 'root'@'localhost'=password('MyNewPass4!');
```

注意：mysql5.7默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。否则会提示ERROR 1819 (HY000): Your password does not satisfy the current policy requirements错误，如下图所示：
通过msyql环境变量可以查看密码策略的相关信息：

```
mysql> show variables like '%password%';
```

  - validate_password_policy：密码策略，默认为MEDIUM策略
  - validate_password_dictionary_file：密码策略文件，策略为STRONG才需要
  - validate_password_length：密码最少长度
  - validate_password_mixed_case_count：大小写字符长度，至少1个
  - validate_password_number_count ：数字至少1个
  - validate_password_special_char_count：特殊字符至少1个

  上述参数是默认策略MEDIUM的密码检查规则。

共有以下几种密码策略：

策略 | 检查规则
--|--
0 or LOW   | Length
1 or MEDIUM |  Length; numeric, lowercase/uppercase, and special characters
2 or STRONG | Length; numeric, lowercase/uppercase, and special characters; dictionary file

MySQL官网密码策略详细说明：http://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_policy

**修改密码策略**

在/etc/my.cnf文件添加validate_password_policy配置，指定密码策略

```
# 选择0（LOW），1（MEDIUM），2（STRONG）其中一种，选择2需要提供密码字典文件
validate_password_policy=0
```
如果不需要密码策略，添加my.cnf文件中添加如下配置禁用即可：

```
validate_password = off
```
重新启动mysql服务使配置生效：
```
systemctl restart mysqld
```
### 6、添加远程登录用户
默认只允许root帐户在本地登录，如果要在其它机器上连接mysql，必须修改root允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，我添加一个新的帐户：

```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'yangxin'@'%' IDENTIFIED BY 'Yangxin0917!' WITH GRANT OPTION;
```

### 7、配置默认编码为utf8
修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置，如下所示：

```
[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
```

重新启动mysql服务，查看数据库默认编码如下所示：

默认配置文件路径：

- 配置文件：/etc/my.cnf
- 日志文件：/var/log//var/log/mysqld.log
- 服务启动脚本：/usr/lib/systemd/system/mysqld.service
- socket文件：/var/run/mysqld/mysqld.pid

