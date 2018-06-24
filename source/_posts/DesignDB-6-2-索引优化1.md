---
title: DesignDB-6-2_索引优化1
date: 2018-06-24 12:22:30
categories: mysql
tags:
    - designdb
    - 索引优化
---

### 索引优化策略

####  索引列上不能使用表达式或者函数

```sql
# 以下对out_date使用了函数,所以是错误的
> select ... from product 
    where to_days(out_date)-to_days(current_date)<=30;

# 优化
> select ... from product
    where out_date<=date_add(current_date,interval 30 day)
```

#### 前缀索引和索引列的选择性

```sql
CREATE INDEX index_name ON TABLE(col_name(n));
```

索引的选择性是不重复的索引值和表的记录数的比值

#### 联合索引

**如何选择联合索引列的顺序**
- 经常用到的列优先选择
- 选择性高的列优先 
- 宽度小的列优先使用

#### 覆盖索引

**优点**
1. 优化缓存,减少磁盘I/O操作
2. 减少随机IO，变随机IO操作为顺序IO操作
3. 可用避免InnoDB主键索引的二次查询
4. 可避免MyISAM表进行系统调用

**缺陷**
- 不是所有搜索引擎都支持覆盖索引
- 查询中使用了太多的列
- 使用了双%的like查询

