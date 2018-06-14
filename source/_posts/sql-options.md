---
title: sql_options
date: 2018-06-14 17:47:55
categories: MySQL
tags:
    - mysql
---
## mysql options

**insert**
```
insert into table_name (column1,column2) values (value1,value2)
```

**delect**

```
delect from table_name where  expr
```

**update**

```
update table_name set column1=value1,column2=value2;
```


**select**

```
Select column1,column2  From table_name  Where expr;
```

## Data Type

整型
```
Tinyint/ smallint/ mediumint/int/ bigint(M) unsigned zerofill
```
小数型
```
Float(M,D),decimal(M,D)
```
字符串型
```
Char(M)
Varchar(M)
Text 文本类型
```
日期时间类型
```
Date 日期
Time 时间
Datetime 时间时间类型
Year 年类型
```
### Create Table

```
create table table_name (
column_name1 column_type1  column_1_param,
column_name2 column_type2 column_2_param,
....
...
column_namen column_typen column_n_param
)engine myisam/innodb/bdb charset utf8/gbk/latin1...
```

### Modify Table

add column
```
#  alter table m1 add birth date not null default '0000-00-00';
Alter table table_name add column_name column_type column_param;                              [add column end of table]

# alter table m1 add gender char(1) not null default '' after username;
Alter table table_name add column_name column_type column_param after column_x                 [add column after column_x]

# alter table m1 add pid int not null default 0 first;
Alter table table_name add column_name column_type column_param first                          [add first column]
```
del column

```
Alter table table_name drop column
```

update column_type
```
# alter table m1 modify gender char(4) not null default '';
Alter table table_name modify column_name column_type  column_param
```
update column_type and column_name
```
# alter table user change  name names varchar(30) not null default '';

alter table table_name change old_column_name new_column_name column_type column_params
```
### 5 Types of Select

#### 1. where


```
Select * from tableName where id >=2;
```

SQL | Description
--|--
like | just like
%   | all words
'_' | single word

where expr is true
the result of select can looks like table.

```
select count(*) form user;
select count(1) from user;              [good]
select uid,sum(age) from user;          [wrong]
```
#### having

limit function like avg/sum atc.


```
# select name ,sum(score) from student  group by username having sum(score)>300 ;
show sum of score bigger than 300 and group by name

# select productid,cateid,avg(price),sum(price)  from shop_product group by cateid  having productid < 1000;
show productid , avg(price),sum(price) from shop_product group by cateid having productid<1000.
```
instead of where is wrong.

#### Order by

```
# select username from qtjk_user where userid<20 order by logintime desc,userid asc limit 0,3;

show
```
#### join

- A left join B = B right join A   [ use left join! ]
- left right inner join


#### backup

#### index

