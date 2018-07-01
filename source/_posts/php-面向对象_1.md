---
title: php_面向对象_1
date: 2018-07-01 08:32:32
categories: php
tags:
    - 面向对象
    - this
    - 封装性
    - 继承性
    - final类和方法
---

>* ### 定义一个对象

```php

class Ren{
    public static $uname = "Miki";
    
    public function __construct(){
        echo "construct on";
    }

    public function walk(){
        $uname = "def";
        echo $this::$uname."&nbsp;walk fast";
    }
}

$ren = new Ren();
echo '<br>';
echo $ren->walk();
```

>* ### 封装mysqli


```php
class Mysql {

    public $link=null;

    function __construct(){
        $cfg = array(
            "host" => "localhost",
            "user" => "root",
            "password" => "",
            "db" => "test",
            "charset" => "utf8"
        );
       return $this->link = mysqli_connect($cfg['host'],$cfg['user'],$cfg['password'],$cfg['db']);
    }

    public function query($sql){
        # return mysqli::query($sql);
        return mysqli_query($this->link,$sql);
    }

    public function getAll($sql){
        $res = $this->query($sql);
        while($row = mysqli_fetch_assoc($res) ){
            $data[] = $row;
        }
        return $data;
    }
}

$db = new Mysql();
var_dump($db->query("desc test"));
```

>* ### 封装性
封装 : 即禁止某些方法/属性 , 不充许外部调用

```php

class ATM
{
    protected function getPass(){
        echo  "123456";
    }

    public function checkPass(){
        return $this->getPass();
    }
}

$user = new ATM();
$user->checkPass(); //123456
$user->getPass(); //Fatal Error
```
这个例子,如果用面向过程的函数来开发,则很难对getPass()做屏蔽

>* ### 继承性
单继承的:子类只能继承一个父类,demo如下

```php
class Father{
    function money(){
        echo "I have 10 yuan";
    }
}

class Son extends Father {
    function money(){
        echo "I have 5 yuan";
    }
}

$f = new Father();
$f->money();

echo "<hr>";
$s = new Son();
$s->money();
```

>* ### Final 类和方法

```php
final class Stu{
    public function think(){
        echo "This class can't be extends";
    }
}

#  class StuSan extends Stu{ } //Final Error
class Stu1{
    final public function demo(){
        echo "Final function can not be rewrited";
    }
}

class Stud2 extends Stu1{
    # public function demo(){   //Final Error
    public function demo1(){
        echo "Rewrite demo";
    }
}

$stu = new Stud2();
echo $stu->demo1();
```
>* ### 3种权限

```php


<?php

class Human{
    public $name = "Json";
    protected $car = "BMW";
    private $gf = "Connie";

    public function sub() {
        echo $this->name."<br/>";
        echo $this->car."<br/>";
        echo $this->gf."<br/>";
    }

}

class Stu extends Human{

    public function par() {
        echo $this->name."<br/>";
        echo $this->car."<br/>";
        echo $this->gf."<br/>";
    }
}

$stu = new Stu();
$stu->sub();
$stu->par();  //Undefined property: Stu::$gf 
echo $stu->name;
echo $stu->car; //Cannot access protected property Stu::$car
echo $stu->gf; //Cannot access protected property Stu::$car 

```

地址|Public|Protected|Privated
--|--|--|--
外部|Y|N|N
子类|Y|Y|N
本子类|Y|Y|Y

**总结**

- public: 到处都能用
- protected: 本类或者子类只要通过方法都能用
- private: 只有通过本类的方法才能调用.(可继承)

