---
title: DesignDB-6-3-索引优化2.md
date: 2018-06-24 12:47:47
categories: mysql
tags:
    - designdb
---

## 使用索引来优化查询

### 使用索引扫描来优化排序

#### 1. 通过索引来优化排序操作
- 索引的列顺序和Order By子句的顺序完全一致
- 索引中所有列的方向(升序降序)和Order By子句完全一致
- Order By中的字段全部在关联表中的第一张表中

```sql
> show create table rental;
> explain select * from rental where rental_date > '2005-01-01' order by rental_id;

# 可见type为index
```

#### 2. 模拟Hash索引来优化查找速度
- 只能处理键值的全值匹配查找
- 所使用的Hash函数决定了索引键的大小

试验步骤
```sql
> show create table film\G;

# 可以见列 `title` varchar(255) NOT NULL,

# 增加1列
> alter table film add title_md5 varchar(32);
# 更新数据为md5的值
> update film set title_md5=md5(title);
# 新建一个索引来模拟hash索引
> create index idx_md5 on film;
# 通过下面语句,通过Btree索引模拟hash索引
> explain select * from film where title_md5=('EGG IGBY')and title='EGG IGBY';

```

#### 3. 利用索引优化锁

- 索引可以减少锁定行数
- 索引可以加快处理速度,同时加快锁的释放

```sql
> show create table actor\G;

# 删除索引idx_actor_last_name
> drop index idx_actor_last_name on actor;

# 随意执行一个查询
> explain select * from actor where last_name='WOOD'\G;

# 开始优化,先执行一个事务
> begin;
# 增加1个排查锁，其他SQL链接则无法访问.
> select * from actor where last_name='WOOD' for update;

# 增加一个索引
> create index idx_last_name on actor(last_name);
# 查看查询计划
> explain select * from actor where last_name='WOOD'\G;
```

#### 4. 索引的维护和优化

1. 删除重复和冗余的索引

```sql
# 重复索引
# 主键索引函括后面的index 和 unique key
> primary key(id),unique key(id),index(id)

# 冗余索引
> index(a),index(a,b);
> primary key(id),index(a,id);
```

2. 用工具检测冗余索引
```
$ pt-duplicate-key-checker h=127.0.0.1
```

3. 查找未被使用过的索引

可以直接复制到sql中,结果会出列出所有表盒索引信息,及其使用次数

```sql
select object_schema,object_name,index_name,b.`table_rows`
    from performanc_schema.table_io_waits_summary_by_index_usage a
    join information_schema.table b ON
    a.`object_shcema`=b.`object_schema` and
    a.`object_schema`=b.`object_name` 
where index_name is not null
and count_star=0
order by object_schema,object_name;
```

4. 更新索引统计信息,以及减少索引碎片

```sql

analyze table table_name

# 下面语句使用不当可能锁表
optimize table table_name;

```

