---
title: laravel 自定义函数和变量
date: 2022-07-15 11:40:38
categories:
	- php
tags:
	- laravel
	- route
---


#### 创建自定义函数

1. 创建文件 app/helpers.php

```php
<?php

// 示例函数
function foo() {
    return "foo";
}

```

2. 修改项目 composer.json

在项目 composer.json 中 autoload 部分里的 files 字段加入该文件：

```json
{
    ...

    "autoload": {
        "files": [
            "app/helpers.php"
        ]
    }
    ...
}

```

3. 然后运行:

```
composer dump-autoload

```

OK，然后你就可以在任何地方用到 app/helpers.php 中的函数了。

#### 创建自定义变量

1. 创建文件 `config/param.php`

```php
<?php

return [
	'aliyun' => [
		'key' => 'xxxx',
		'secret' => 'xxxxxx'
	]
];

```

2. 得到数据

```
use Illuminate\Support\Facades\Config;

public function test(){
	echo Config::get('param.aliyun.key');
}

```
