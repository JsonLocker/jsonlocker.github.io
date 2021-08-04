---
title: composer境内镜像
date: 2019-11-16 10:34:19
categories: php
tags:
    - composer
---

## 加速原理

Composer 安装时候会向国外的 Packagist 服务器发送请求，因为众所周知的原因，国内请求国外服务器，有时会出现不稳定甚至不可用的情况。

镜像加速就是把国外的数据缓存到国内的服务器上，Composer 客户端只需配置服务器为国内的服务器，即可从国内服务器上下载，稳定性会有很高的提升。

## 调用方式

全局模式

```
$ composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

单独项目使用：

```
$ composer config repo.packagist composer https://mirrors.aliyun.com/composer/
```

#### 阿里云 Composer 全量镜像（推荐） 

- 镜像类型：全量镜像
- 更新时间：1 分钟
- 镜像地址：https://mirrors.aliyun.com/composer/
- 官方地址：https://developer.aliyun.com/composer
- 镜像说明：阿里云 CDN 加速，更新速度快，推荐使用

#### 安畅网络镜像

- 镜像类型：全量镜像
- 更新时间：1 分钟
- 镜像地址：https://php.cnpkg.org
- 官方地址：https://php.cnpkg.org/
- 镜像说明：此 Composer 镜像由安畅网络赞助，目前支持元数据、下载包全量代理。

#### 交通大学镜像

- 镜像类型：非全量镜像
- 镜像地址：https://packagist.mirrors.sjtug.sjtu.edu.cn/
- 官方地址：https://mirrors.sjtug.sjtu.edu.cn/packagist/
- 镜像说明：上海交通大学提供的 composer 镜像，稳定、快速、现代的镜像服务，推荐使用。


