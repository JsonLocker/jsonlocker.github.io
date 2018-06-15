---
title: DesignDB-2-04_操作系统配置
date: 2018-06-15 17:25:31
categories: mysql
tags:
    - designdb
    - OS
---


## MySQL适合的操作系统

- Windows
    - 注意大小写,window大小写不敏感
- Linux
- FreeBS
- Solaris

## CentOS系统参数优化

### 内核相关参数(/etc/sysctl.conf)
可以下内容直接写入文件

#### **网络参数**
```
#决定每个端口监听队列的长度
net.core.somaxconn=65535
#允许发送到对列数据包的数目
net.core.netdev_max_backlog=65535
net.ipv4.tcp_max_syn_backlog=65535

# 加快tcp连接的内存回收
net.ipv4.tcp_fin_timeout=10
net.ipv4.tcp_tw_reuse=1
net.ipv4.tcp_tw_recycle=1

# tcp连接缓冲区大小值和最大值
net.core.wmem_default = 87380
net.core.wmem_max = 16777216
net.core.rmem_default = 87380
net.core.rmem_max = 16777216

# 减少实效链接占用tcp资源
net.ipv4.tcp_keepalive_time = 120
net.ipv4.tcp_keepalive_intvl = 30
net.ipv4.tcp_keepalive_probes = 3
```
#### **内存参数**(/etc/sysctl.conf)
```
# Linux内核参数最重要的参数之一,用于定义单个共享内存段的最大值
# 需要设置足够大,以便能容纳下整个InnoDB缓冲池的大小
# 建议取物理内存的一半,最大可为物理内存值－1byte，下面是取4个G的大小
kernel.shmmax = 4294967295

# 对于内存不足时,对性能产生比较明显影响
# 除非虚拟系统完全占满了,否则不要使用交换区
vm.swappiness = 0
```

#### **资源限制**(/etc/security/limit.conf)
limit.conf是Linux PAM 是插入式认证模块的配置文件

```
# 打开文件数量
# * 对所有用户有效
# soft对当前系统有效
# 表示系统所能设定的最大值 
# 所限制打开文件的最大数量
# 限制的数值
# 需要重启系统生效
* soft nofile 65535
* hard nofile 65535

```

#### **磁盘调度策略**(/sys/block/devname/queue/scheduler)

使用下面的命令进行查看当前磁盘的调度策略

```bash
cat /sys/block/sda/queue/scheduler
noop anticipatory deadline [cfq]

# 把调度策略改成deadline
echo deadline > /sys/block/sda/queue/scheduler

```
noop(电梯式调度策略)
当一个新请求到来,将请求合并到最久的请求之后，以此来保证请求同一介质。

NOOP倾向饿死读而利于写,因此NOOP对**闪存设备.RAM**以及**嵌入式系统**是最好的选择.

deadline(截止时间调度策略)
确保一个截至时间内服务请求，这个截至时间可调整，默认读期限短于写期限，这样可以避免写操作因为不能读取而饿死的现象。
deadline对于**数据库类**是最好的选择.

anticipatory(预料I/O调度策略)
和deadline类似,在最后一次读操作后要等待6s,再进行其他I/O请求调度。适合**写入较多**的环境,比如文件服务器,它对数据库环境表现很差

### 扩展
内存交换区

在安装Linux系统时候会有个特殊的磁盘分区，叫系统交换分区
用`free-m`命令查看可以看到 swap 就是交换分区
当系统没有足够内存时就会将虚拟内存写到磁盘交换区中进行内存交换
mysql优化上说是希望可以禁用，

禁用的负面作用
- 降低操作系统性能
- 容易造成内存溢出，崩溃或者都被系统kill掉

### 文件系统

Windows
    - FAT
    - NTFS*
Linux
    - EXT3
    - EXT4
    - XFS*

EXT3/4 系统的文件挂载参数(/etc/fstab)

data = writeback | ordered | journal
 - writeback (最快)
 - ordered(慢,安全)
 - journal(没必要，最慢)

# 禁止纪录访问文件的时间和读取目录的时间
# 节省写操作
noatime,nodiratime 

```
# 完整的配置
/dev/sda1/ext4 noatime,nodiratime,data=writeback 1 1
```
