---
title: tinyproxy代理
date: 2019-09-20 10:04:07
categories:
	- linux
tags:
	- tinyproxy
	- http proxy
	- proxy
---

### install tinyproxy

```
yum install tinyproxy -y
```

### config

```
vim /etc/tinyproxy/tinyproxy.conf

# 端口
Port 8888
# 允许访问的ip，后面接允许的ip，比如我只需要我阿里云的公网ip能访问就行，于是用阿里云公网ip替换127.0.0.1。如果要所有ip均可访问就注释掉此配置
Allow 127.0.0.1
# 性能配置，一般不需要修改，根据需要进行调整。（这里我没有修改）
MaxClients 100
MinSpareServers 5
MaxSpareServers 20
StartServers 10
MaxRequestsPerChild 0
MaxRequestsPerChild 0
```

### system command

```
# start
systemctl start tinyproxy

# restart
systemctl restart tinyproxy.service

```

### firewall

```
firewall-cmd --add-port=8888/tcp --permanent  # 开发的端口为你上面配置的端口
firewall-cmd --reload
```
