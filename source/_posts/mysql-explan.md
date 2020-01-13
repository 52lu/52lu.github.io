---
title: Mysql性能优化神器之Explain使用
date: 2018-06-03 18:53:53
tags: 
 - mysql
categories:
 - 数据库 
---

### 1.什么是explain?
MySQL提供了一个explain命令,这个命令可以查看一个SQL语句的执行计划、有没有使用上了索引、有没有做全表扫描、以及查询的类型等。

### 2.使用示例
```sh
mysql> EXPLAIN (SELECT * from hs_user WHERE uid in (1,2)) UNION (SELECT * from hs_user WHERE uid in (3,4,5));
+------+--------------+------------+-------+---------------+---------+---------+------+------+-----------------+
| id   | select_type  | table      | type  | possible_keys | key     | key_len | ref  | rows | Extra           |
+------+--------------+------------+-------+---------------+---------+---------+------+------+-----------------+
|    1 | PRIMARY      | hs_user    | range | PRIMARY       | PRIMARY | 50      | NULL |    2 | Using where     |
|    2 | UNION        | hs_user    | range | PRIMARY       | PRIMARY | 50      | NULL |    3 | Using where     |
| NULL | UNION RESULT | <union1,2> | ALL   | NULL          | NULL    | NULL    | NULL | NULL | Using temporary |
+------+--------------+------------+-------+---------------+---------+---------+------+------+-----------------+
3 rows in set (0.01 sec)
```
### 3.explain输出字段
- id: SELECT 查询的标识符. 每个 SELECT 都会自动分配一个唯一的标识符.
- select_type: SELECT 查询的类型.
- table: 查询的是哪个表
- type: join 类型
- possible_keys: 此次查询中可能选用的索引
- key: 此次查询中确切使用到的索引.
- key_len:表示索引中使用的字节数
- ref: 哪个字段或常数与 key 一起被使用
- rows: 显示此查询一共扫描了多少行. 这个是一个估计值
- extra: 额外的信息

### 4.id 查询的标识符
SELECT查询的标识符. 每个SELECT都会自动分配一个唯一的标识符,id如果相同，可以认为是一个分组，从上往下顺序执行,id值越大，表示优先级越高，越先执行。

### 5.select_type: SELECT查询的类型.
![](https://52lu.github.io/directionsImg/mysql/explan-selectType.png)

### 6.table:表示查询涉及的表或衍生表

### 7.type: 显示访问类型，是较为重要的一个指标。
![](https://52lu.github.io/directionsImg/mysql/explan-type.png)

从最好到最差依次是：system >const >eq_ref >ref >range >index >ALL。一般来说，得保证查询至少达到range级别，最好能达到ref。


### 8.possible_keys: 可能使用的索引
显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用。

### 9.key: 实际使用的索引
实际使用的索引。如果为NULL，则没有使用索引。查询中若使用了覆盖索引，则该索引仅出现在key列表中

### 10.key_len:表示索引中使用的字节数
表示索引中使用的字节数，可通过该列计算查询中使用的索引长度。在不损失精确性的情况下，长度越短越好。key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的。
 
### 11. ref: 哪个字段或常数与key一起被使用
显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列表或常量被用于查找索引列上的值
**如下SQL会显示为常数**
```sh
mysql> EXPLAIN SELECT * from hs_user WHERE uid = 1;
+----+-------------+---------+-------+---------------+---------+---------+-------+------+-------+
| id | select_type | table   | type  | possible_keys | key     | key_len | ref   | rows | Extra |
+----+-------------+---------+-------+---------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | hs_user | const | PRIMARY       | PRIMARY | 50      | const |    1 | NULL  |
+----+-------------+---------+-------+---------------+---------+---------+-------+------+-------+
1 row in set (0.01 sec)
```

### 12. rows:扫描了多少行
显示此查询一共扫描了多少行. 这个是一个估计值.

### 13.extra: 额外的信息
![](https://52lu.github.io/directionsImg/mysql/explan-extra.png)
### 14.Explain的作用
- 表的读取顺序
- 数据读取操作的操作类型
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询