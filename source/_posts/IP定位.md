---
title: IP定位
date: 2019-09-17 16:06:04
categories:
	- html
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


