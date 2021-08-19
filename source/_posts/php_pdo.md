---
title: php pdo
date: 2019-10-11 15:49:19
categories: php 
tags:
    - mysqld 
    - pdo
    - register
---

CODE

```php

Class Db {

  private static $_db = null;
    private function __construct(){
    self::$_db  = new PDO('mysql:host=127.0.0.1;dbname=tp6', 'root', 'root');
  }


  private static function _get_db(){
    if(self::$_db == null ){
      $obj = new self();
    }
    return self::$_db;
  }

  private static function _back_msg($errcode, $errmsg){
    die( json_encode(['code'=>$errcode,'errmsg'=>$errmsg]) );
  }

  public static function register($uname, $pwd){
    $db = self::_get_db();

    $query = $db->prepare("select count(*) from user where `username` = ?");
    $query->execute([$uname]);
    $count = $query->fetchAll();

    if(intval( $count[0]['count(*)']) > 0 ){
      self::_back_msg(1005,'username is exists !');
    }

    if( strlen($pwd) < 8 ){
      self::_back_msg(1006,'password is short than 8 !');
    }else{
      $password = self::_password_generate($pwd);
    }

    $query = $db->prepare("insert into `user` (username,password,age) VALUES (?,?,?)");
    $res = $query->execute([$uname, $password, 21]);
    if(!$res){
      self::_back_msg(1006,'write into db error!');
    }
    return true;
  }

  private static function _password_generate($pwd){
    return md5($pwd);
  }

}

Db::register('dd1ab','ccsssssssssssssssssss');
```
