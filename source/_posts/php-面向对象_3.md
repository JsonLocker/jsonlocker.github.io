---
title: php_面向对象_3
date: 2018-07-01 08:32:32
categories: php
tags:
    - 面向对象
    - 自动加载
    - 抽象类
    - 接口
---

>* ### 自动加载
声明一个函数,并注册为"自动加载函数". 当系统发现某个类不存在时,会调用此函数,我们可以在函数中加载需要的类文件.

```php
function myLoad($class){
    echo "autoload".$class;
    require("./".$class.".php");
}

//把参数myload函数注册成自动加载函数
//当后续发现未知类名,把类名作为参数给myload执行
spl_autoload_register("myLoad");
new mysql();
```

>* ### 抽象类

- 类前要加abstract,则为抽象类
- 方法前也可以加abstract ,则为抽象方法
- 抽象方法没有方法体
- 抽象类中也可以有已经实现的方法,但,只有要1个方法为抽象,则类仍是抽象的 抽象类不能实例化

```php

/**
* 定义一个大模板
* 模板中对 方法名,参数,返回值,都做严格的规定
*/
abstract class aDB{

    /*
    * 参数: sql,sql语句
    * 返回值类型: string
    */

    abstract public function getRow($sql);

    /*
    * 参数: sql,sql语句
    * 返回值类型: string
    */
    abstract public function getAll($sql);

    
    /*
    * 参数: sql,sql语句
    * 返回值类型: string
    */
    abstract public function Exec($sql);
}


/*
* 具体实施模板
* 上面每个方法都要实现,不能遗漏
*/
class MySQL extends aDB{

    public function getRow($sql){

    };

    public function getAll($sql){

    };


    abstract public function Exec($sql){

    };

}
```

>* ### 接口

- 接口本身就是抽象的,方法前不用加abstract
- 接口里的方法,只能是public
- 类可以同时实现多个接口

> 注:抽象类,相当于一类事物的规范;接口:组成事物的零件的规范



