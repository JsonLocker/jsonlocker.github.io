---
title: Static Page
date: 2018-06-27 14:58:56
categories: lnmp
tags:
    - nginx
    - static
    - rewrite
---

### 1.流程
1. 动态语言流程

```
graph LR
A[syntax]-->B[complie]
B-->C[run]
C-->D[show]
```

2. 动态程序执行顺序
```
graph LR
A[ConnectDB]-->B[Get Date]
B-->C[file Template]
C-->D[Show]
```

**静态页面展示**
```
graph LR
run--> show
```

### 2. Buffer
buffer其实是php的缓冲区，一个内存地址空间，主要用于存储数据区域.

php的展示流程

```
graph LR
A[content]-->B[php buffer]
B-->C[tcp]
C-->D[screen]
```

**开启的2种方式**

```
# php.ini
output buffering=on

# page.php
ob_start();
```

### 3. 页面静态化实现

#### 3.1 实现方式

>* file_put_contents()函数
```php
# file_put_contents('目标静态存储文件','原动态页面文件')
file_put_contents('./index.html', './index.php');
```
>* output buffering

函数|备注
--|--
ob_start | 打开输出控制缓冲
ob_get_contents | 返回输出缓冲区内容
ob_clean | 清空（擦掉）输出缓冲区
ob_get_clean | 得到当前缓冲区的内容并删除当前输出缓冲区


#### 3.2 静态化页面 Demo

> 1. 设计动态页面
```php
# template.php

$arr = [
    [ 1,'title1','content1'],
    [ 2,'title1','content1'],
    [ 3,'title1','content1'],
    [ 4,'title1','content1'],
    [ 5,'title1','content1'],
];

# 页面展现
foreach($arr as $t){
    echo $t[0];
    echo $t[1];
    echo $t[2];
}
```
> 2. 生成静态

```php
ob_start();

require_once('./template.php');

# 直接生成一个静态化页面

if( file_put_contents('template./html', './.php') ){
    echo 'success';
}else{
    echo 'false';
}
```
### 4. 静态化触发方式

>* 手动触发
>* 页面缓存触发
>* 定时器触发



>* 手动触发

- 设置更新按钮
- 变动内容自动触发

>* 页面缓存触发

原理
```
graph TD

A[用户请求页面]-->B{判断 }
B --> |存在且未过期|E[直接访问静态页面]
B --> |过期/不存在| D[动态页面并生成新的静态页面]
```
实现
```php
# 获取文件修改时间
// $ftime = filemtime('./index.html');

# 如果文件存在且小于5分钟,则直接访问
if( is_file('index.html') && ( $time()-$filemtime('./index.html'))<300 ){
    require('index.html');
}else{
    # 文件不存在或者失效则用缓存生成页面并展示给用户
    ob_start();
    require('template.php');
    file_put_contents( 'index.html',ob_get_contents() );
}
```
>* crontab 定时触发

```bash
# 分 时 日 月 周
# 每5分钟执行1次代码
crontab -e

*/5 * * * * * php /pathto/index.php

```

### 5. 伪静态 及 Rewrite配置

原本URL: `http://state.com/newsList.php?type=2&category_id=1`

目标网址 `http://state.com/newsList.php/2/1.html`

分析可得只要修改以下部分并拼接即可

```
2=> type=2  1=>category_id=1
```

#### 5.1  伪静态


```php
# preg_match( 表达式,原链接,结果 )
$res = preg_match('/\/(\d+)\/(\d+)\.html/',$_SERVER['PATH_INFO'],$arr);
echo '<pre>';
var_dump($arr);
echo '</pre>';

if( $res ){
    $type = $arr[1];
    $category_id = $arr[2];

}else{
    //TODO
}
print_r($arr);
```

> 注意: Nginx的网址参数是 $_SERVER['REQUEST_URI']

### 6. 伪静态配置

#### 6.1 Apache的配置

1. httpd.conf文件中开启相关模式
2. LoadModule rewrite module modules/mod rewrite.so
3. Include conf/extra/httpd-vhosts.conf
4. vhosts文件增加相关域名

```
# vhosts.conf


RewriteEngine on

# 如果有文件存在,则不重写
RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME}!-d
RewriteCond %{DOCUMENT_ROOT}%{REQUEST_FILENAME}!-f

# 配置伪静态
RewriteRule ^/detail/([0-9]*).html$/detail.php?id=$1
```
#### 6.2 Nginx的伪静态配置



```
# vhost.conf

location ~\.php{
    if (!-e $request_filename){
        rewrite ^/detail/([0-9]*).html$  /detail.php?id=$1 last;
        break;
    }
}
```

reload nginx
```
service nginx restart
```
