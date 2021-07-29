---
title: url美化
date: 2021-07-29 16:48:38
categories: yii2
tags:
    - yii2
    - urlmanager
---

1. nginx

```
location / {
  try_files $uri $uri/ index?$args
}

```

2. config


```
# common/config/main.php

'components' => [
  'urlManager' => [
    'enablePrettyUrl' => true,
    'showScriptName' => false,
    'rules' => [
       '<controller:(index|cart|order)>' => '<controller>/index',
       'auth' => 'memeber/auth',
       'post-<postid:\d+>' => 'post/detail',
       [
       	 'pattern' => 'newurl',
	 'route' => 'admin/default/index',
	 'suffix' => '.html'
       ]
    ]
  ]
]


```
