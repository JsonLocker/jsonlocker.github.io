---
title: apcu
date: 2021-10-15 16:19:56
categories:
    - php
tags:
    - apuc
---

PHP APCu是php的一个缓存扩展，用户缓存

```
pecl install apcu
```

相关函数

```
// APCu的数据存储中检索缓存的信息
apcu_cache_info();

// 检索APCu共享内存分配信息
apcu_sma_info();

// APCu key信息
apcu_key_info();

// 当前环境APCu是否可用
apcu_enabled();

// 设置一个缓存，没有失效时间。再次apcu_add()同一个key，值不会覆盖
apcu_add();

// 删除指定key
apcu_delete();

// 获取指定key缓存
apcu_fetch();

// 设置一个缓存，带有失效时间。失效后还会占用内存空间，需使用apcu_delete()才可以彻底删除。
apcu_store();

// 更新一个key的值
apcu_cas();

// 自增
apcu_inc();

// 自减
apcu_dec();

// 判断key是否存在
apcu_exists();

// 以原子方式获取或生成缓存
apcu_entry();

// 清除全部缓存
apcu_clear_cache();
```


实例

```
<?php
// 添加一个缓存，没有失效时间。再次apcu_add()同一个key，值不会覆盖
apcu_add("bool", false);
apcu_add("string", "string");
apcu_add("int", 999);
apcu_add("float", 99.99);
apcu_add("array", [1, 2, 3, 4, 5]);

// APCu的数据存储中检索缓存的信息
var_dump(apcu_cache_info());

// 检索APCu共享内存分配信息
var_dump(apcu_sma_info());

// key信息
var_dump(apcu_key_info("float"));
var_dump(apcu_key_info("string_store"));

// 当前环境apcu是否可用
var_dump(apcu_enabled());

// 删除指定key
var_dump(apcu_delete("bool"));

// 设置一个缓存，失效时间单位为秒。时间可选，默认永不失效（非重启）
var_dump(apcu_store("bool_store", FALSE, 5));
var_dump(apcu_store("string_store", "string", 10));
var_dump(apcu_store("int_store", 999, 15));
var_dump(apcu_store("float_store", 99.99, 20));
var_dump(apcu_store("array_store", [1, 2, 3, 4, 5], 25));

// 更新一个key的值
$old = 1;
$new = 2;
apcu_add("cas", $old);
var_dump(apcu_cas("cas", $old, $new));

// 自增
apcu_add("inc", 1);
$success = false;
var_dump(apcu_inc("inc", 10, $success));
apcu_fetch("inc");
var_dump($success);

// 自减，可以为负数
apcu_add("dec", 100);
$success = false;
var_dump(apcu_dec("dec", 10, $success));
apcu_fetch("dec");
var_dump($success);
```


