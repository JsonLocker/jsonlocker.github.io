---
title: Compile_lnmp
date: 2018-06-28 15:51:28
categories: linux
tags:
    - mysql
    - linux
    - compile
    - lnmp
---


### 1.安装 Nginx

#### 1.1 安装依赖

```
yum -y install gcc gcc-c++ autoconf automake make
yum -y install  zlib-devel
```

#### 1.2 下载,解压,编译
```
wget http://nginx.org/download/nginx-1.15.0.zip
tar -zxvf nginx-1.15.0.tar.gz
cd nginx-1.15.0
./configure --prefix=/usr/local/nginx
```

#### 1.3 设置软链并启动

```
ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
nginx -h
nginx
```

#### 1.4 配置 nginx

```
# /etc/local/nginx/conf/nginx.conf

server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.php index.html index.htm;
        }


        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }


        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $DOCUMENT_ROOT$fastcgi_script_name;
            include        fastcgi_params;
        }
...
}

```

```
ln -s /usr/local/nginx/html  ~
```

### 2. 安装 php


#### 2.1 下载解压

```
wget http://cn2.php.net/distributions/php-7.2.7.tar.gz
tar -zxvf php-7.2.7.tar.gz
cd php-7.2.7
```
#### 2.2 安装依赖
```
yum install gcc
yum install libxml2
yum install libxml2-devel
yum install -y curl-devel
yum install openssl openssl-devel
yum install libpng-devel
yum install freetype-devel
yum install libxslt-devel* -y
```

#### 2.3 安装 PHP

```
./configure --prefix=/usr/local/php \
--with-curl \
--with-gd \
--with-gettext \
--with-iconv-dir \
--with-kerberos \
--with-libdir=lib64 \
--with-libxml-dir \
--with-mysqli \
--with-openssl \
--with-pcre-regex \
--with-pdo-mysql \
--with-pdo-sqlite \
--with-pear \
--with-png-dir \
--with-xmlrpc \
--with-zlib \
--enable-fpm \
--enable-bcmath \
--enable-libxml \
--enable-inline-optimization \
--enable-mbregex \
--enable-mbstring \
--enable-opcache \
--enable-pcntl \
--enable-shmop \
--enable-soap \
--enable-sockets \
--enable-sysvsem \
--enable-xml \
--enable-zip
```

#### 2.4 make

```
make
make install
```

#### 2.5 配置PHP
```
cp php.ini-development /usr/local/php/lib/php.ini

cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf

cp -R  /root/download/php-7.2.7/sapi/fpm/php-fpm  /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
/etc/init.d/php-fpm
```


### 3.安装 MySQL

#### 3.1 安装依赖

```
yum install libaio-devel ncurses-devel cmake gcc gcc-c++ bison -y
```

#### 3.2 安装 MySQL
```
# 下载
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
# 校验校验码
md5sum mysql57-community-release-el7-9.noarch.rpm
# 安装
sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm
sudo yum install mysql-server
```

#### 3.3 启动MySQL

1. 启动
```
sudo systemctl start mysqld
sudo systemctl status mysqld
```
> Note: MySQL is automatically enabled to start at boot when it is installed. You can change that default behavior with sudo systemctl disable mysqld

2. 查看默认密码
```
sudo grep 'temporary password' /var/log/mysqld.log

# 2018-06-28T07:07:04.914495Z 1 [Note] A temporary password is generated for root@localhost: XTk;w145PkgT
```

#### 3.4 配置MySQL

3. 登录
```
mysql -uroot -p XTk;w145PkgT
```
4. 查看密码配置
```
show variables like "%password%";
```
5. 设置密码配置长度和安全  设置新密码
```
set global validate_password_length=0;
set global validate_password_policy=low;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
exit;
```
6. 设置远程登录权限
默认只允许root帐户在本地登录，如果要在其它机器上连接mysql，必须修改root允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，我添加一个新的帐户：

```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'yangxin'@'%' IDENTIFIED BY 'Yangxin0917!' WITH GRANT OPTION;
```

7. 配置默认编码为utf8

修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置，如下所示：
```
[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
```

重新启动mysql服务，查看数据库默认编码如下所示：

- 配置文件：/etc/my.cnf
- 日志文件：/var/log//var/log/mysqld.log
- 服务启动脚本：/usr/lib/systemd/system/mysqld.service
- socket文件：/var/run/mysqld/mysqld.pid

