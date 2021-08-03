---
title: v2ray
date: 2020-03-06 17:25:50
categories: other
tags:
    - v2ray
    - software
    - vmess 
---

### `V2Ray_ws-tls_bash_onekey`


基于 Nginx 的 vmess+ws+tls 一键安装脚本 （Use Path）。部署速度快，但相较之multi-v2ray缺少了定制以及用户组等功能。 增加不同的配置需要手动更改配置文件。因此适合自用。

如果你选择使用 V2Ray，强烈建议你关闭并删除所有的 shadowsocksR 服务端，仅使用标准的 V2Ray 三件套。

#### 安装步骤

1. Vmess+websocket+TLS+Nginx+Website 优化版本

```
wget -N --no-check-certificate -q -O install.sh "https://raw.githubusercontent.com/paniy/V2Ray_ws-tls_bash_onekey/master/install.sh" && chmod +x install.sh && bash install.sh
```

优化版说明（2020年2月26日）：

- 可以直接输入命令：idleleo管理脚本（修复了部分反映无法使用的bug）
- 减少直接访问，隐藏代理域名，302跳转至www.idleleo.com（了解配置过程可自行修改；只支持ws协议）
- 阻止HTTP直接访问服务器IP，更安全（只支持ws协议）
- 优化tls 1.3加密配置 （只支持ws协议）
- 减少无关元素

2. Vmess + websocket + TLS + Nginx +网站（原版）

```
wget -N --no-check-certificate -q -O install.sh "https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh" && chmod +x install.sh && bash install.sh
```

#### 启动方式
启动 V2Ray：`systemctl start v2ray`

启动 Nginx：`systemctl start nginx`

开机自动启动 V2Ray：`systemctl enable v2ray` （新版已集成）

开机自动启动 Nginx：`systemctl enable nginx` （新版已集成）

升级V2Ray core 执行：
```
 bash <(curl -L -s https://install.direct/go.sh)
```

#### 配置防火墙

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --reload
```

> [Github 上的优化版](https://www.idleleo.com/go?url=https://github.com/paniy/V2Ray_ws-tls_bash_onekey)
> [Github 上的原版](https://www.idleleo.com/go?url=https://github.com/wulabing/V2Ray_ws-tls_bash_onekey)
> [参考](https://www.idleleo.com/09/2148.html)
