---
title: php_面向对象_2
date: 2018-07-01 08:32:32
categories: php
tags:
    - 面向对象
    - 静态属性和方法
    - 类常量
    - 单例模式
    - self & parent

---

>* ### 静态属性和对象- static

无需生成对象,通过类名字直接调用其属性和方法.

```php
class Math{
    public static version = "1.03";

    public static function add($num1,$num2){
        return $num1 + $num2;
    }

    public static function sub($num1,$num2){
        return $num1 -$num2;
    }
}

$m1 = new Math();
$m2 = new Math();

echo Math::add(1,2);  //3
echo Math::$version;  //1.03
```

>* ### 类常量 - Const

就是类里面的全局常量,优点说防止定义的名和全局常量冲突

```php

define('PI',3.14159);

class Math{
    const PI = 3.14;

    public static function gp(){
        echo PI+1;
    }
}

$math = new Math();

echo $math::PI;
echo '<br>';
echo $math::gp();
```

>* ### 单例模式

一个类只能定义一个对象

```php
/*
 * 1. 构建一个普通类
 * 2. 用private contruct 限制外部new
 * 3. 内部静态实例化(非静态报错)
 * 4. 把实例化函数设置final防止重写
 */

final class Single {
    static protected $ins;

    protected function __construct(){
        $this->rand = mt_rand(10000,99999);
    }

    public static function getIns() {
        if(Single::$ins === null){
            Single::$ins = new Single();
        }
        return Single::$ins;
    }
}

$a = Single::getIns();
$b = Single::getIns();

print_r($a);
print_r($b);
```

>* ### Self & Parent

- self 代表本类
- parent 代表父类
- $this 代表本对象


**self**

```php
class Single{
    static protected $ins = null;

    protected function __construct(){
        $this->rand = mt_rand(10000,99999);
    }

    public static function getIns(){
        if (self::$ins === null){
            self::$ins = new self();
        }
            return self::$ins;
    }
}

var_dump(Single::getIns());
var_dump(Single::getIns());
```
**parent**

```php
class Human{
    protected function __construct(){
        echo mt_rand(10000,99999);
    }
}

class Stu extends Human{

    public function __construct(){
        //new parent();
       echo parent::__construct();
    } 

}
new Stu();
```

>* 魔术方法

阻止随意向类里面写入方法和属性.会报一个NOTICE错误.

- __construct(): 构造方法,new 实例时,自动调用
- __destruct(): 析构方法,对象销毁时自动调用
- __get(属性名): 当读取对象的一个不可见属性时,自动调用,并返回值
- __set(属性名,属性值): 当对一个不可见的属性赋值时,自动调用
- __isset(属性名): 当用isset,或empty判断一个不可见属性时,自动调用
- __unset(属性名): 当unset一个不可见属性时,自动调用

> 不可见: 未定义或者无权访问

```php
class demo{
    public function __construct(){
        echo "construct is done<br>";
    }
    public function __get($data){
        echo $data;
        echo "<br>";
    }
    public function __set($a,$b){
        echo "set".$a."value".$b;
        echo "<br>";
    }
    public function __isset($data){
        echo "exists".$data;
        echo "<br>";
    }
    public function __unset($data){
        echo "unset value".$data;
        echo "<br>";
    }
    public function __destruct(){
        echo "desctruct is done";
    }

}

$a = new demo();
$a->sublime;
$a->name = 123;
isset($a->json);
unset($a->connie);

///////////////////////////////////////

class Bar{
    public function __set($name,$val){
        echo "you have no power here";
    }
}

$bar = new Bar();
$bar->age = 9;  //Undefined property: Bar
``
