---
title: OptimizerMySQL_06_索引
date: 2018-06-24 14:27:11
categories: mysql
tags:
    - optimizer
    - index
    - mysql
---

### MySQL语句优化

通过Explain可以分析,mysql是如何执行语句

```sql
Explain select * from emp where ename='zrlcHd';


         id: 1         
select_type: SIMPLE         # 查询类型
      table: emp            # 输出结果集的表
       type: ALL            # 表示表的连接类型
possible_keys: NULL         # 表示查询时，可能使用的索引
        key: NULL           # 表示实际使用的索引
    key_len: NULL           # 索引字段的长度
        ref: NULL           # 
       rows: 4400000        # 扫描出的行数
      Extra: Using where    # 执行情况和描述说明
1 row in set (0.00 sec)

ERROR: 
No query specified
```

### 如何增加索引

**索引**

- 极大提升查询效能
- 代价是降低了插入删除和编辑的效率
－增大系统的IO开销

**4种索引:** 
- 主键索引 
- 唯一索引
- 全文索引
- 普通索引

#### 1. 添加索引

##### 1.1 主键索引的添加
方法1:
```sql
# 创建表时添加
create table table_name(id int unsigned auto_increment primary key,name varchar(20) not null default '');

# 创建表后添加
alter table table_name add primary key(column_name);
```

##### 1.2 普通索引的添加
一般是先创建表再添加普通索引
```sql
create table ccc (id int unsigned,name varchar(22) );

# create index 索引名 on 表(列1,列2);
create index my_index on ddd(title,name);
alter table table_name add index(column_name);

```
#### 1.3 全文索引的创建
全文索引针对MyISAM索引引擎有效

```sql
# 创建全文索引
create table article(id unsigned,title varchar(20),body text, FULLTEXT(title,body))engine=MyISAM charset=utf8;

# 错误的全文索引使用方法
select * from  article where body like '%mysql%';

# 正确用法
select * from article where match(title,body) against ('datebase');
```
**说明**
- 在mysql中fulltext索引只针对myisam生效
- 只针对英文字母生效,用sphinx(coreseek)技术来支持中文
- 使用方法是用 `match column_name against keywords`
- 全文索引有个停止词概念
```sql
# 会生成1个概念词的概率表
select match(title,body) against ('database') from article \G;
```

#### 1.4 唯一索引
- 某列被指定为唯一约束unique时
- 允许为NULL且允许多个null


```sql
# 创建表生成唯一索引,name列
create table ddd(id int primary key auto_increment,name varchar(20) not null unique);

# 插入唯一索引
# create unique index index_name  on table_name(column_name);
create unique index my_uni on ddd(title);
```


#### 2. 查询索引

方法1:
```sql
# 缺陷是无法显示索引名
desc table_name 无法显示索引名字
```

方法2:
```sql
show index(es) from table_name;
```

方法3*
```sql
show keys from table_name;
```


#### 3. 删除索引

```sql
alter table table_name drop index_name;
```

如果删除主键索引则可用
```sql
alter table table_name drop primary key;
```

#### 4. 修改索引

先删除再重新创建
