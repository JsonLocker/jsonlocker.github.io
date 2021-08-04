---
title: encrypt
date: 2019-09-09 08:54:06
categories: linux
tags:
    - ssl
    - encrypt
    - https
---

## 功能
https证书，免费版，每三个月续签一次，可以用过脚本自动续签

## 安装
1. ssh登录到域名配置所在的主机（nginx，apache等）
2. **安装Nginx的ssl模块**
    1. 切换到nginx安装包
    2. 配置 `./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module`
    3. `make` 重新编译，不需要make  install安装。否则会覆盖
    4. 备份原nginx `mv /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak`
    5. 覆盖`cp ./objs/nginx /usr/local/nginx/sbin/`
    6. 查看模块 `nginx -V`
2. 安装 `git yum -y install git`
3. 输入 
4. ```
5. // git clone https://github.com/letsencrypt/letsencrypt
6. git clone https://github.com/JsonLocker/certbot
4. cd letsencrypt
5. chmod +x letsencrypt-auto
6. ```
6. **安装证书:**
    1. `./letsencrypt-auto certonly --email 邮箱 -d 网址//报错时执行2`
    2. `certbot certonly --webroot -w 网站根目录 -d 网址 -m 邮箱 --agree-tos`
    3. d参数后面对应的是域名，在执行的过程中，我遭遇了下面的报错：
        ```
        Total size: 44 M Downloading Packages: Running rpm_check_debug ERROR with rpm_check_debug vs depsolve: libgdbm.so.2()(64bit) is needed by python-libs-2.6.6-66.el6_8.x86_64 ** Found 7 pre-existing rpmdb problem(s), 'yum check' output follows: 4:perl-5.10.1-141.el6_7.1.x86_64 has missing requires of libgdbm.so.2()(64bit) 4:perl-devel-5.10.1-141.el6_7.1.x86_64 has missing requires of gdbm-devel polkit-0.96-5.el6_4.x86_64 has missing requires of libeggdbus-1.so.0()(64bit) 2:postfix-2.6.6-6.el6_5.x86_64 has missing requires of libmysqlclient.so.16()(64bit) 2:postfix-2.6.6-6.el6_5.x86_64 has missing requires of libmysqlclient.so. 16(libmysqlclient_16)(64bit) 2:postfix-2.6.6-6.el6_5.x86_64 has missing requires of mysql-libs python-libs-2.6.6-52.el6.x86_64 has missing requires of libgdbm.so.2()(64bit) Your transaction was saved, rerun it with: yum load-transactiontmp/.yum_save_tx-2017-04-07-22-1798AqLE.yumtx Could not install OS dependencies. Aborting bootstrap!
        ```
    4. centos 6 需要安装 libgdbm.so.2：
        1. `wget http://mirror.centos.org/centos/6/os/x86_64/Packages/gdbm-1.8.0-39.el6.x86_64.rpm`
        2. `yum localinstall gdbm-1.8.0-39.el6.x86_64.rpm`
        
    5. 完成后，重新执行上面的命令行
    
    
6. **安装**
```
./letsencrypt-auto certonly --email jsonlocker.gmail.com -d school.jsonlocker.com


./letsencrypt-auto certonly  -d dxzs.jsonlocker.com --register-unsafely-without-email -d dxzs.jsonlocker.com

# 提示
How would you like to authenticate with the ACME CA?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: Apache Web Server plugin (apache)
2: Nginx Web Server plugin (nginx) [Misconfigured]
3: Spin up a temporary webserver (standalone)
4: Place files in webroot directory (webroot)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

#选择4 然后输入地址即可
Input the webroot for school.jsonlocker.com: (Enter 'c' to cancel): /usr/local/nginx/html/test/school/frontend/web


# 得到结果
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/school.jsonlocker.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/school.jsonlocker.com/privkey.pem
   Your cert will expire on 2019-04-30. To obtain a new or tweaked
   version of this certificate in the future, simply run
   letsencrypt-auto again. To non-interactively renew *all* of your
   certificates, run "letsencrypt-auto renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le

```

### 配置
    
1. nginx.conf中添加
```
listen 443 ssl;
ssl on;
ssl_certificate /etc/letsencrypt/live/XXX.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/XXX.com/privkey.pem;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
```

2. 重定向https:

在http的server里增加
```
rewrite ^(.*) https://$host$1 permanent;
```
也或者用301跳转

```
if ($server_port = 80 ) {
    return 301 https://$host$request_uri;
}

```


3. 重启 `nginx -s reload`


### 续约

在 `crontab -e`中添加以下代码

```
`06 06 * * * /www/web/test/lets/certbot-master/certbot-auto renew   --force-renewal   --pre-hook "/etc/init.d/nginx stop" --post-hook "/etc/init.d/nginx start" >> /www/web_logs/letsencry.log 2>&1`
```

规定每月的5号执行更新

 `00 05 01 * * /letsencrypt/certbot-auto renew --force-renewal --pre-hook "/etc/init.d/nginx stop" --post-hook "/etc/init.d/nginx start" >> /www/web_logs/letsencry.log 2>&1`

