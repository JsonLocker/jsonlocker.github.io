---
title: sql_miss_index
date: 2022-02-08 14:00:34
categories:
	- mysql
tags:
	- sql
	- index
---

## 1. 准备工作

1.11 创建数据表 `user`

```sql
CREATE TABLE `user` (
  `id` int NOT NULL AUTO_INCREMENT,
  `code` varchar(20) COLLATE utf8mb4_bin DEFAULT NULL,
  `age` int DEFAULT '0',
  `name` varchar(30) COLLATE utf8mb4_bin DEFAULT NULL,
  `height` int DEFAULT '0',
  `address` varchar(30) COLLATE utf8mb4_bin DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_code_age_name` (`code`,`age`,`name`),
  KEY `idx_height` (`height`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin

```
此外，还创建了三个索引：
- `id`：数据库的主键。
- `idx_code_age_name` ：由code、age和name三个字段组成的联合索引。
- `idx_height` ：普通索引。


插入一些数据
```sql
INSERT INTO sue.user (id, code, age, name, height, address) VALUES (1, '101', 21, 'Lucy', 175,'香港');
INSERT INTO sue.user (id, code, age, name, height, address) VALUES (2, '102', 18, 'Jack', 173,'台湾');
INSERT INTO sue.user (id, code, age, name, height, address) VALUES (3, '103', 23, 'Anddy', 174,'成都');
```

1.12 测试

```
explain select * from user where id=1;

```

执行可以看到结果,用到`主键索引`

![primary key](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxQJRO75biaUHrCCIzWWLJZA5aXZIb06jQMK0G8EurAl78sRUyr4XsV1g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 2. 最左匹配原则

#### 2.1 哪些情况索引有效

之前已经给code、age和name这3个字段建好联合索引：`idx_code_age_name`, 

索引顺序是:

1. code
2. age
3. name

==如果在使用联合索引时，没注意最左前缀原则，很有可能导致索引失效。==

```sql

explain select * from user where code='101';
explain select * from user where code='101' and age=21;
explain select * from user where code='101' and age=21 and name="anndy";
explain select * from user where code='101' and name="anndy";
```

![img](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxHGjkWPmBePwI2hTrSLIg6unUic58VsiamdqccpKqfxHe7MVicsrqEicRrA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

结论:

这4条sql中都有code字段，它是索引字段中的第一个字段，也就是最左边的字段。只要有这个字段在，该sql已经就能走索引。

也就是 ==最左匹配原则==

#### 2.2 哪些情况索引失效

```sql

explain select * from user where age=21;
explain select * from user where name='Lucy';
explain select * from user where age=21 and  name='Lucy';
```

![img](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxgNqNibGR2iaSWgLDKr3hAUy967Zyjkgjlyn17yANY1A0lqTkCRg9APHg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从图中看出这3种情况下索引失效了。因为查询条件中，没有包含给定字段最左边的索引字段，即字段code。

## 3. 使用了select *


```sql
explain select * from user where name='苏三';

```

![img](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxwBB9icbgjQt3ubMTLhjtLibicIb61NGQ4bOZicrLMgRBkqsWN7H0JzA0nQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在该sql中用了 `select * ` ，从执行结果看，走了全表扫描，没有用到任何索引，查询效率是非常低的。

如果改成只查code 和 name 列

```sql

explain select code,name from user where name='苏三';

```
![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxqfHBchA4FwqxordoOw63M4GqC6iaGrz9581fW31yiacLxdwiaEjiaMPCvw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从图中执行结果不难看出，该sql语句这次走了全索引扫描，比全表扫描效率更高。

其实这里用到了：==覆盖索引==。

## 4. 索引列上有计算

如果id列上面有计算，比如：

```sql

explain select * from users where id+1=2;

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxFE50mCCOiaW2icHczMAKy2icQqNzfFuiajb3qqbnFXB9TdOcHMsUgZLuibg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从上图中的执行结果，能够非常清楚地看出，该id字段的主键索引，在有计算的情况下失效了。

## 5. 索引列用来函数

```sql 

# ok
explain select * from user  where height=17;


# lost
explain select * from user  where SUBSTR(height,1,2)=17;

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxibYLtLJY8914t6w2kvzicoauoWhVKCJCtdjhicjTYY9EcqUkP0PHAWo5w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在使用函数后，该sql语句竟然走了全表扫描，索引失效了。

## 6. 字段类型不同

```sql

# ok
explain select * from user where code="101";

# lost
explain select * from user where code=101;

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxZ54Oj4Q6fnDHc10zgmIicZnDksibWDtCqT5DzysgQ2ziawJiaJEbhiaoYTg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

第二个sql语句变成了全表扫描。因为少写了引号，这种小小的失误，竟然让code字段上的索引失效了。

问：为什么索引会失效呢？

答：因为code字段的类型是varchar，而传参的类型是int，两种类型不同。

如果 `int`类型，不管有咩加冒号，都可以正常走索引

```sql
#ok
explain select * from user where height=175;

#ok
explain select * from user where height='175';

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxN3AzwRmR7OKPE7GnAicdlbyqzeoSm9vImnqtKUAZoktR2VawQnBz67g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1_)

说明: `int`类型字段作为查询条件时，它会自动将该字段的传参进行隐式转换，把字符串转换成int类型。


## 7.  like左边包含%

```sql

#ok 
select * from user where name like '李%';

#lost
explain select * from user where code like '%1';

#lost
explain select * from user where code like '%1%';

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxzzyCNkXMJsNEJ4KRkfyFJibUPJSD4kwdhibfe1qPibOvaOAicib5tEdlXEw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


从图中看出这种%在1左边时，code字段上索引失效了，该sql变成了全表扫描。

==索引就像字典中的目录。一般目录是按字母或者拼音从小到大，从左到右排序，是有顺序的。==

## 8. 列对比

假如我们现在有这样一个需求：

过滤出表中某两列值相同的记录。比如user表中id字段和height字段，查询出这两个字段中值相同的记录。

```sql
explain select * from user where id=height
```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxATykTtpHcIEib2ib2tvIXZYnsHYL1K7f8S59AbSxyR9devtIlR3zE63w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

意不意外，惊不惊喜？索引失效了。

id字段本身是有主键索引的，同时height字段也建了普通索引的，并且两个字段都是int类型，类型是一样的。

但如果把两个单独建了索引的列，用来做列对比时，索引会失效。

## 9. 使用or关键字

一个需求：想查一下id=1或者height=175的用户。

```sql
#ok
explain select * from user where id=1 or height='175';

#lost

explain select * from user where id=1 or height='175' or address='成都';

```
![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxFJKUW01NVwsjSwqONS8D6Ljex32QzTT2yibz95AsgEXAbW3QnpoPbvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

因为最后加的address字段没有加索引，从而导致其他字段的索引都失效了。

## 10. not in和not exists

在我们日常工作中用得也比较多的，还有范围查询，常见的有：

- in
- exists
- not in
- not exists
- between and


10.1  not in

假如我们想查出height在某些范围之内的用户，这时sql语句可以这样写：

```sql

#ok
explain select * from user where height in (173,174,175,176);
explain select * from user  t1  where  exists (select 1 from user t2 where t2.height=173 and t1.id=t2.id)
```

关键字是走了索引的。


```sql
#ok
explain select * from user
where id  not in (173,174,175,176);

#lost
explain select * from user
where height not in (173,174,175,176);
```
![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxMj9sUzEnFHLEjdGpHHOA4enB0ibOR4O3pyD4QZibl9icwjHMdS0ctC15w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1_)

==主键字段中使用not in关键字查询数据范围，仍然可以走索引。而普通索引字段使用了not in关键字查询数据范围，索引会失效。==


10.2 not exists

除此之外，如果sql语句中使用not exists时，索引也会失效。具体sql语句如下：

```sql

explain select * from user  t1
where  not exists (select 1 from user t2 where t2.height=173 and t1.id=t2.id)

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARx0FwkHHm2fJQwnGPjVYvgVUPZUn4PYcekvicWjaCRTwvWUvRT3kEa2nQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从图中看出sql语句中使用not exists关键后，t1表走了全表扫描，并没有走索引。


10.3 order by

```sql


#ok
explain select * from user order by code limit 100;
explain select * from user order by code,age limit 100;
explain select * from user order by code,age,name limit 100;

```
![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxNmINw94jaF576u7kUmo0VuXPibe4lVPuibJSz0eAM9ic0T9uEJMswpVzg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

order by后面的条件，也要遵循联合索引的最左匹配原则。

除了遵循最左匹配原则之外，有个非常关键的地方是，==后面还是加了limit关键字，如果不加它，索引会失效。==


10.4 配合where一起使用

```sql

#ok
explain select * from user where code='101' order by age;
explain select * from user where code='101' order by name;


```

虽说name是联合索引的第三个字段，但根据最左匹配原则，该sql语句依然能走索引，因为最左边的第一个字段code，在where中使用了。只不过order by的时候，排序效率比较低，需要走一次filesort排序罢了。

```sql
explain select * from user order by code desc,age desc limit 100;
explain select * from user where code='101' order by code, name;

```
依然走了索引。

## 11. 哪种情况不走索引

11.1 没加where或limit

```sql
explain select * from user order by code, name;

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxEsoVqloBRCENzF6eEf3O9iaqTwhvrMGMYa8DpmnNJHfEUJun6o7iaa1w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

order by语句中没有加where或limit关键字，该sql语句将不会走索引。

11.2 对不同的索引做order by


前面基本都是联合索引，这一个索引的情况。但如果对多个索引进行order by，结果会怎么样呢？

```sql
explain select * from user
order by code, height limit 100;

```

![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxEsoVqloBRCENzF6eEf3O9iaqTwhvrMGMYa8DpmnNJHfEUJun6o7iaa1w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
索引失效了。
 

11.3 不满足最左匹配原则

```sql
explain select * from user order by name limit 100;

```
![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxKibqx24Ba74ljuZgTEHeFOLJtJYpkuYWWcdIXY8AcQHHVia4kpQv5RZw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

name字段是联合索引的第三个字段，从图中看出如果order by不满足最左匹配原则，确实不会走索引。

11.4 不同的排序

```sql

explain select * from user
order by code asc,age desc limit 100;

```
![](https://mmbiz.qpic.cn/mmbiz_png/ibJZVicC7nz5ianHlF0AzOr530aPCbgeARxcNICl7kPMwXKrPfUHZVkImKkJWNibFZqia4mZA0qOQMRMzzqRrz9QO8g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

从图中看出，尽管order by后面的code和age字段遵循了最左匹配原则，但由于一个字段是用的升序，另一个字段用的降序，最终会导致索引失效。

