---
title: shadowsocks
date: 2019-09-14 17:06:07
categories:
tags:
	- shadowsocks
	- shadowsocks-libev
---
目前 shadowsocks 中还在不断更新的只有 shadowsocks-libev，本文主要介绍这个。

### shadowsocks-libev
ubuntu 的安装很简单，一条命令即可，不过貌似对 Ubuntu 版本有要求
sudo apt install shadowsocks-libev
安装的可能不是最新版，最新版请手动编译

### CentOS Install
按官网说明安装软件，建议采用手动方法，原因一是能保证用的是最新版，二是出了问题容易上网搜索解决。
[website](https://github.com/shadowsocks/shadowsocks-libev)

按下面进行无脑操作。。

```
yum install epel-release -y
yum install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel libsodium-devel mbedtls-devel -y

git clone https://github.com/shadowsocks/shadowsocks-libev.git
cd shadowsocks-libev
git submodule update --init --recursive

./autogen.sh && ./configure && make
sudo make install
```

接下来需要将配置文件拷贝到常用路径
添加配置文件：

```
cp ./rpm/SOURCES/etc/init.d/shadowsocks-libev /etc/init.d/shadowsocks-libev
cp ./debian/config.json /etc/shadowsocks-libev/config.json
chmod +x /etc/init.d/shadowsocks-libev
```

### Single User

编辑 `vim /etc/shadowsocks-libev/config.json`
```
{
    "server":["[::0]","0.0.0.0"],
    "server_port":8388,
    "local_port":1080,
    "password":"barfoo!",
    "timeout":60,
    "method":"chacha20-ietf-poly1305"
}
```
该配置同时支持 Ipv6 和 Ipv4，并采用 ss3.0 支持的 AEAD 加密方式。
编辑 `/etc/init.d/shadowsocks-libev` 修复一些历史bug

```
// old
DAEMON=/usr/bin/ss-server

// new 
DAEMON=/usr/local/bin/ss-server
```

修改权限并重启软件

```
chmod 755 /etc/init.d/shadowsocks-libev
/etc/init.d/shadowsocks-libev start
```

### multi user

多用户配置有多种方法，官方推荐通过给每个用户一个配置文件，启动多个ss-server实现。
还有种方法如下，通过启动ss-manager实现
多用户配置文件与单用户不同，我们配置文件目录创建一个`ss_manager.json`

```
{
    "server": ["[::0]","0.0.0.0"],
    "local_port": 1080, # 本地端口，自行设定
    "timeout": 600, # 超时毫秒数
    "method": "chacha20-ietf-poly1305",
    "port_password": {
        "8388": "barfoo1", # 端口号与密码
        "8389": "barfoo2"
    }
}
```
### 设置开机启动

```
chkconfig --add shadowsocks
service shadowsocks start
```

若是针对多用户情况，需要手写开机启动脚本，方法如下：
应在`/usr/lib/systemd/system`下新建一个.service文件，例如 `ssmanager.service`

```
[Unit]
Description=shadowsocks manager # 服务的描述
After=network.target # 该服务跟在哪个服务后启动

[Service]
Type=forking # 启动时的进程行为，forking是以fork形式从父进程创建子进程，子进程创建后父进程退出
ExecStart=/usr/local/bin/ss-manager --manager-address /var/run/shadowsocks-manager.sock -c /etc/shadowsocks/ss_manager.json # 启动服务时执行的命令
PrivateTmp=true # 使用私有临时文件目录

[Install]
WantedBy=multi-user.target # 附挂在multi-user.target下
```

所有配置文件与脚本都设置完毕，使用systemctl开启服务。

```
# 更改.service文件后，都要重载守护进程更新systemctl
systemctl daemon-reload
# 启动服务
systemctl start ssmanager.service
# 查看服务状态，若为绿色active(running)则说明服务成功启动
systemctl status ssmanager.service
# 设置服务开机自启动
systemctl enable ssmanager.service
# 还可使用systemctl restart ssmanager.service重启服务，使用systemctl stop ssmanager.service停止服务
```
### 开启防火墙白名单

很多人按照教程的配置完后，发现并不能使用，原因是端口被防火墙关闭了。
输入如下代码开启防火墙端口白名单：
```
vim /etc/firewalld/zones/public.xml
```
在文件中适当位置添加下面代码：

```
<port protocol="tcp" port="[server port]"/>
<port protocol="udp" port="[server port]"/>
```
`[server port]` 填你的端口号。
重启防火墙

```
firewall-cmd --complete-reload
```

### 开启多进程

shadowsocks-libev 一个进程只能开启一个端口，因此我们要开启多个进程，对应每个进程使用一份相应的配置文件，从而达到多用户使用的目的
配置完多份配置文件后w在 /etc/init.d/shadowsocks 相应位置添加如下代码，一下代码仅供参考，读者可根据实际需求修改，这些代码的意思都很简单。

```
# 配置文件位置
CONF1=/etc/shadowsocks-libev/config1.json

# 检测配置文件是否存在
if [ ! -f $CONF1 ] ; then
    echo "The configuration file1 cannot be found!"
    exit 0
fi

# 定位到 pid 文件
PID1=/var/run/$NAME/pid1

# 开启进程
daemon $DAEMON -c $CONF1 -f $PID1

# 杀死进程
killproc -p ${PID1}

# 移除 pid 文件
rm -f ${PID1}
```
在原来的脚本里有跟上面代码极其相似的地方（数字1没有），把上面的6段代码插入到原来脚本中对应相似的代码后面即可。


[client](https://github.com/shadowsocks)

[page](https://medium.com/@invisprints/shadowsocks-%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%90%AD%E5%BB%BA-bd5e0b73c4d7)



