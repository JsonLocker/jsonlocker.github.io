---
title: IP定位
date: 2019-09-17 16:06:04
categories:
	- php
tags:
	- locating
	- ip
---

## 获得IP

通过以下代码可获得客户端IP

```php
function getUserIpAddr(){
    if(!empty($_SERVER['HTTP_CLIENT_IP'])){
        //ip from share internet
        $ip = $_SERVER['HTTP_CLIENT_IP'];
    }elseif(!empty($_SERVER['HTTP_X_FORWARDED_FOR'])){
        //ip pass from proxy
        $ip = $_SERVER['HTTP_X_FORWARDED_FOR'];
    }else{
        $ip = $_SERVER['REMOTE_ADDR'];
    }
    return $ip;
}

echo 'User Real IP - '.getUserIpAddr();

```

然后外接[api接口](https://github.com/zoujingli/ip2region)

通过composer获取下资源

```php
composer require zoujingli/ip2region
```
以下代码即可获取到了

```php
$ip2region = new Ip2Region();

$ip = '101.105.35.57';

$info = $ip2region->btreeSearch($ip);

var_dump($info);


// array (
//     'city_id' => 2163,
//     'region' => '中国|华南|广东省|深圳市|鹏博士',
// )

```

## 调用纯真IP库

[github地址](https://github.com/itbdw/ip-database)

引入
```
composer require 'itbdw/ip-database'
```
调用

```
//根据实际情况，基本上用框架（如 Laravel）的话不需要手动引入
//require 'vendor/autoload.php';

use itbdw\Ip\IpLocation;

//支持自定义文件路径
$qqwry_filepath = '/abspath/qqwry.dat';
echo json_encode(IpLocation::getLocation($ip, $qqwry_filepath), JSON_UNESCAPED_UNICODE) . "\n";
echo json_encode(IpLocation::getLocation($ip), JSON_UNESCAPED_UNICODE) . "\n";
```
相应

```
{
  "ip": "163.177.65.160",
  "country": "中国",
  "province": "广东",
  "city": "深圳市",
  "county": "",
  "isp": "联通",
  "area": "中国广东省深圳市腾讯计算机系统联通节点"
}
```
测试

```
php tests/ip.php -i 58.196.128.0 

```
在线直接更新

```
# 更新到源码目录 
php ~/bin/update-ip.php

# 更新到指定目录 php ~/bin/update-ip.php -d /tmp
```

<b>自己手动更新数据库</b>

1，http://www.cz88.net/fox/ipdat.shtml 下载数据库程序（Windows 环境），执行完毕后，即可在程序安装目录找到数据库文件 qqwry.dat

2，复制到 src 目录，覆盖掉原文件即可；或者，把文件同步到服务器特定路径，但这种方式要求调用方法时传入 qqwry.dat 的绝对路径。

