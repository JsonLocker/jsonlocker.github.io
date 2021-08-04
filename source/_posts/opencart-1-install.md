---
title: opencart_1_install
date: 2021-08-03 14:26:52
categories: php
tags:
    - opencart
    - install
---

# install opencart

1. download [file](https://github.com/opencart/opencart/releases)


2. upload server
```
mkdir opcart
rz opencart-3.0.3.7.zip
unzip opencart-3.0.3.7.zip
cd upload
mv dist-config.php config.php
mv admin/dist-config.php  admin/dist-config.php
```
3. config nginx set & set ssl `op.czzor.com`

4. 

```
Move

/www/wwwroot/czzor/opencart/upload/system/storage/

to

/www/wwwroot/czzor/opencart/storage/

Edit config.php change

define('DIR_STORAGE', DIR_SYSTEM . 'storage/');

to

define('DIR_STORAGE', '/www/wwwroot/czzor/opencart/storage/');

Edit admin/config.php and change

define('DIR_STORAGE', DIR_SYSTEM . 'storage/');

to

define('DIR_STORAGE', '/www/wwwroot/czzor/opencart/storage/');

```

visit:

- [frontend](cp.czzor.com)
- [backend](cp.czzor.com/admin)


