---
title: 跨域CORS
date: 2021-08-05 14:33:21
categories:
tags:
    - cors
---

在非IE下，使用XMLHttpRequest 不能跨域访问， 除非要访问的网页设置为允许跨域访问。

将网页设置为允许跨域访问的方法如下：


## PHP

```
echo：header（"Access-Control-Allow-Origin: *"）;

echo：header（"Access-Control-Allow-Origin: http://www.1688hot.com:80"）;

```

## HTML

```
<meta http-equiv="Access-Control-Allow-Origin" content="*">

<meta http-equiv="Access-Control-Allow-Origin" content="http://www.1688hot.com:80">

```

在返回的响应信息中，使用 Access-Control-Allow-Origin 头来控制哪些域名的脚本可以访问该资源。

如果设置 Access-Control-Allow-Origin:*，则允许所有域名的脚本访问该资源。如果有多个，则只需要使用逗号分隔开即可。

注意：在服务器端，Access-Control-Allow-Origin 响应头中的端口信息不能省略。
