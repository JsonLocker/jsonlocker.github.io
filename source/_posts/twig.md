---
title: twig
date: 2021-08-04 13:49:21
categories: php
tags:
    - twig
---

## install twig

```
composer  search twig
composer require twig/twig
```


## create view

```
mkdir view
touch view/index.html
touch index.php
```

## editor index.php

```php
require('vendor/autoload.php');

$loader = new Twig_Loader_Filesystem('view');
$twig = New Twig_Environment($loader);

echo $twig->render('hello.html', array(
    'name' => 'json',
    'age' => 12,
    'users' => [
        ['name' => 'miki', 'age' => 33],
        ['name' => 'lucy', 'age' => 23]
    ]
));

```

## editor index.html

```html
hello, {{name}} , age is {{age}}

<hr>


{% if age > 10 %}
    {{name}} is not smaller than 10 years old <br/>
{% endif %}


{% for user is in users %}
    name is {{user.name}} and age is {{users.age}}
{% endfor %}

```
