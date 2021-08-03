---
title: ssr的obfs混淆免流
date: 2019-11-15 15:52:41
categories:
	- linux
tags:
	- ssr
---

 ## 服务器安装

1.  大猫猫

```bash
# centos7
yum install wget -y && wget https://raw.githubusercontent.com/SuicidalCat/Airport-toolkit/master/ssr_node_c7.sh && chmod +x ssr_node_c7.sh && ./ssr_node_c7.sh

# centos8
dnf install wget -y && wget https://raw.githubusercontent.com/SuicidalCat/Airport-toolkit/master/ssr_node_c8.sh && chmod +x ssr_node_c8.sh && ./ssr_node_c8.sh

# uninstall
systemctl disable ssr_node && \rm /usr/lib/systemd/system/ssr_node.service && \rm -rf /soft/shadowsocks

# start
systemctl start ssr_node

# stop
systemctl stop ssr_node
```

2. 秋水大佬

大佬被喝茶已不更新，且用且珍惜

- 1.加密方式推荐 chacha20-itef-poly1305,
- 2.端口可以尽量设置高一点,避免443, 1080等常用端口,
- 3.安装 simple-obfs , 选择 http 模式

```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log

# uninstall
./shadowsocks-all.sh uninstall

# start
etc/init.d/shadowsocks-libev start

# stop
/etc/init.d/shadowsocks-libev stop

# restart
/etc/init.d/shadowsocks-libev restart

# status
/etc/init.d/shadowsocks-libev status

```

[SS obfs-local插件下载地址](https://github.com/shadowsocks/simple-obfs/releases)

## 2.  注意事项

配置文档类似以上，注意如下标注参数
```
[
    {
        "d": 0,
        "enable": 1,
        "forbidden_port": "25,465,233-266",
        "method": "aes-256-cfb",
        "obfs": "http_simple",                    # 混淆方式
        "passwd": "a9679I4b5S4k3e0R",             # 链接密码
        "port": 443,                              # 端口号
        "protocol": "auth_aes128_md5", #协议
        "speed_limit_per_con": 0,
        "speed_limit_per_user": 0,
        "transfer_enable": 9007199254740992,
        "u": 0,
        "user": "443"
    }
]
```

以上有两个重点，其一就是端口号。一般免流端口都建议为80或者443。如非这两个端口，就需要在混淆参数上做功夫，后文再说。

混淆方式为http_simple是为了让运营商检测到你访问的网址是免流host，如果是tls那么运营商无法识别，是不会被算作免流请求的。

## 客户端配置

参照服务器配置，基本一致。

obfs参数（混淆参数）

如果是80或者443端口,混淆参数填写以下网址之一即可：

```
tx.flv.huya.com
short.weixin.qq.com
szextshort.weixin.qq.com
short.weixin.qq.com
```

如果是非80及443，如下配置

```
szminorshort.weixin.qq.com#Upgrade: mmtlsnAccept: /nConnection: closenContent-Length: 533nContent-Type: application/octet-stream
```
也可自行抓包，找免流HOST，然后填写到混淆参数即可。
