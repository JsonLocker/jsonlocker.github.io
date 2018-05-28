---
title: 1-2-2_Javascript
date: 2018-05-28 18:50:04
tags:
---

# Javascript

## 1. 概述

加载方式
```
<script>
function changetext(id){
    id.innerHTML="THANK YOU!";
}
</script>

<h1 onclick=""changetext(this)"> Hello World</h1>
```

获取html元素及内容及修改
```
document.getElementById("myid") .innerHTML="hello world"
```

获取动态内容
```
document.write(date())
document.write( "<iframe src='http://www.baidu.com'></iframe>");
```

增加页面互动

```
<h1 onclick=""changetext(this)"> Hello World</h1>
```


## 2.DOM
连接web页面和编程语言

## 3.BOM
连接浏览器和编程语言

常用的弹窗，用于调试和信息展示
```
alert ("警告");
confirm ("确认");
prompt ("提示");
```

获取用户COOKIE
```
document.cookie
```
修改COOKIE
```
document.cookie="test=sa";
```

```
(window.)screen     # 获取浏览器屏幕信息
(window.)location   # 获取用户的url
    window.location.href="http://www.baidu.com"  #改变url跳转
(window.)navigator  # 获取访问者用户的浏览器信息

window.open("http://www.baidu.com")   #打开链接
window.close("http://www.baidu.com")  #关闭链接
```

