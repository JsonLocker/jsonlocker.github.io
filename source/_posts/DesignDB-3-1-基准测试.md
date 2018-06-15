---
title: DesignDB-3-1_基准测试
date: 2018-06-15 17:06:32
categories: mysql
tags:
    - designdb
    - mysql
    - 基准测试
---

### 基准测试
简称: 针对系统设置的压力测试

描述:是一种测量和评估软件性能指标的活动,用于建立某个时刻的性能基准,以便系统发生软硬件变化时候重新进行基准测试以评估变化对性能的影响。

**基准测试**
- 直接
- 简单
- 易于比较
- 用于评估服务器的处理能力
- 不关心业务逻辑


**压力测试**
- 对真实业务数据进行测试
- 获得真实系统所承受的压力
- 针对不同的主题,所使用的数据和查询也真实用到的

### 如何进行基准测试

#### 目的

- 建立MYSQL服务器的性能基准线
- 模拟比当前系统更高的负载,找到系统扩展瓶颈
    - 一般增加数据库并发,观察QPS,TPS变化,确定并发量和性能最优的关系
- 测试不同硬件,软件,操作系统配置
- 测试新的硬件设备是否配置正确

#### 基准测试方法

1. 对整个系统进行基准测试
    - 对系统入口进行测试(如网站web前端,手机app前端)

优点:
    - 能测试整个系统的性能,包括web服务器缓存,数据库等
    - 能反应出系统中各个组件接口间的性能问题,体现真实性能状态

缺点:
    - 测试设计复杂,消耗时间很长

2. 单独对MySQL进行基准测试

优点:
    - 设计测试简单,耗费时间短

缺点:
    - 无法全面了解整个系统的性能基线


#### MySQL基准测试的常见指标

测量数据库的每秒吞吐量
    - 单位时间内所处理的事务数(TPS)
    - 单位时间内所处理的查询数(QPS)

    - 响应时间
        - 平均响应时间
        - 最小响应时间
        - 最大响应时间
        - 各时间所占百分比(最关注)

    - 并发量:同时处理的查询请求数量
        - 不等于连接数,注意！
        - 正在工作中的并发操作数或同时工作的数量(关注点)

### 基准测试步骤

#### 1. 基准测试的步骤

    - 对整个系统还是某组件
    - 使用什么数据进行基准测试
        - 时间.次数尽可能大,求平均让数据更靠谱
        - 内容: 可用生产环境的SQL日志回放进行基准测试
    - 准备基准测试及数据收集脚本
        > 内容包括: CPU使用率.IO.网络流量.状态与计数器信息等
        > 方法: 用慢查询收集数据 .  用软件生成
    - 运行基准测试
    - 保存及分析基准测试

基准测试的脚本

```bash
#!/bin/bash
    INTERVAL=5
    PREFIX=/home/imooc/benchmarks/$INTERVAL-sec-status
    RUNFILE=/home/imooc/benchmarks/running
    echo "1" > $RUNFILE
    MYSQL=/usr/local/mysql/bin/mysql
    $MYSQL -e "show global variables" >> mysql-variables
    while test -e $RUNFILE; do
        file=$(date +%F_%I)
            sleep=$(date +%s.%N | awk '{
                        print 5 - ($1 % 5)
                        }')
    sleep $sleep
        ts="$(date +"TS %s.%N %F %T")"
            loadavg="$(uptime)"
                echo "$ts $loadavg" >> $PREFIX-${
                    file
                }-status
    $MYSQL -e "show global status" >> $PREFIX-${
        file
    }-status &
    echo "$ts $loadavg" >> $PREFIX-${
        file
    }-innodbstatus
    $MYSQL -e "show engine innodb status" >> $PREFIX-${
        file
    }-innodbstatus &
    echo "$ts $loadavg" >> $PREFIX-${
        file
    }-processlist
    $MYSQL -e "show full processlist\G" >> $PREFIX-${
        file
    }-processlist &
    echo $ts
    done
    echo Exiting because $RUNFILE does not exists
```

信息QPS分析脚本

```bash
#!/bin/bash
awk '
   BEGIN {

            printf "#ts date time load QPS";
                 fmt=" %.2f";
                    
   }
   /^TS/ {

          ts = substr($2,1,index($2,".")-1);
             load = NF -2;
                diff = ts - prev_ts;
                   printf "\n%s %s %s %s",ts,$3,$4,substr($load,1,length($load)-1);
                      prev_ts=ts;
                         
   }
   /Queries/{

          printf fmt,($2-Queries)/diff;
             Queries=$2
                    
   }
   ' "$@"

```

