---
title: opencart_2_安装插件
date: 2021-08-04 11:02:23
categories: php
tags:
    - opencart
---

扩展插件就是在原程序的基础上再增加一些其他已经有的功能扩展，并且几乎不需要改动原程序代码，安装后即可实现功能，列如语言包插件、支付类型插件等；而修改插件则需要在原程序上改动并生成
新文件才能达到新增功能或者修改功能的效果，安装后需要更新ocmod缓存。

1. 下载[安装中文语言包插件](https://www.opencart.com/index.php?route=marketplace/extension/info&extension_id=19126&filter_search=language&filter_category_id=2&filter_license=0&filter_download_id=49)

2. 官方说明

```
path System -> Localisation -> Languages -> Insert

fill in with

Language Name: 简体中文
Code: zh_cn
Locale: zh_CN.UTF-8,zh_CN,zh-cn,china
Image: cn.png
Directory: zh-CN
Filename: zh-CN
Status: Enabled (启动)
Sort Order: 任意数字

填写后按 Save 存档

2、setting default language as default

select：Sytem -> Settings -> Local

Language: 简体中文
Administration Language: 简体中文

then Save


```

3.  自我描述

登录opencart后台 打开插件安装页面 上传插件，上传成功之后会有提示，在这个上传步骤中它做了解压文件、记录插件文件路径 和安装插件记录等这些步骤，
我们可以根据路由 marketplace/installer 找到对应的文件去查看它的具体执行步骤。

安装成功之后 还需要去设置：

```
System ->Localisation -> Languages  ->点击新增 ->添加信息 ->保存
Locale: zh,zh-hk,zh-cn,UTF-8,cn-gb,chinese        (语言代码红色字体)
```

网站语言设置  ：

```
System -> setting -> 编辑  -> local  -> 选择语言 -> 保存

```


