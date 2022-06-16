---
title: 简单工厂
date: 2021-06-16 17:06:31
categories: patterns 
tags:
    - php 
    - patterns
    - simple factory
---

## 简单工厂

类如果直接都用new,那有一天命名需要改动的时候得搜索每一行代码去改，而工厂避免了这种情况

会让我们的代码更具结构化，便于管理和扩展。


比如，我们有一些类，它们都继承自交通工具类：

```php

interface Vehicle
{
    public function drive();
}

class Car implements Vehicle
{
    public function drive()
    {
        echo '汽车靠四个轮子滚动行走。';
    }
}

class Ship implements Vehicle
{
    public function drive()
    {
        echo '轮船靠螺旋桨划水前进。';
    }
}

class Aircraft implements Vehicle
{
    public function drive()
    {
        echo '飞机靠螺旋桨和机翼的升力飞行。';
    }
}

```

再创建一个工厂类，专门用作类的创建，：

```php

class VehicleFactory
{
    public static function build($className = null)
    {
        $className = ucfirst($className);
        if ($className && class_exists($className)) {
            return new $className();
        }
        return null;
    }
}

```

工厂类用了一个静态方法来创建其他类，在客户端中就可以这样使用：

```php

VehicleFactory::build('Car')->drive();
VehicleFactory::build('Ship')->drive();
VehicleFactory::build('Aircraft')->drive();
```

省去了每次都要new类的工作。


