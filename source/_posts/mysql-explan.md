---
title: explan使用分析
date: 2018-06-03 18:53:53
tags: 
 - mysql
categories:
 - DB 
---

### Sql 示例


```
mysql> explain select * from user;

+----+-------------+-------+------+---------------+------+---------+------+------+-------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
|  1 | SIMPLE      | user  | ALL  | NULL          | NULL | NULL    | NULL |    4 |       |
+----+-------------+-------+------+---------------+------+---------+------+------+-------+
1 row in set (0.00 sec)
```


### Sql 分析

- #### id 查询的标识符
>SELECT 查询的标识符. 每个 SELECT 都会自动分配一个唯一的标识符,id如果相同，可以认为是一个分组，从上往下顺序执行,id值越大，表示优先级越高，越先执行。

- #### select_type: SELECT 查询的类型.
![](https://mrliuqh.github.io/directionsImg/mysql/explan-selectType.png)

- #### table: 查询的是哪个表
<!--more-->
- #### partitions: 匹配的分区

- #### type: 显示访问类型，是较为重要的一个指标。
![](https://mrliuqh.github.io/directionsImg/mysql/explan-type.png)

> 从最好到最差依次是：system >const >eq_ref >ref >range >index >ALL

<font color=red>一般来说，得保证查询至少达到range级别，最好能达到ref </font>


- #### possible_keys: 可能使用的索引
`显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用。`

- #### key: 实际使用的索引
`实际使用的索引。如果为NULL，则没有使用索引。查询中若使用了覆盖索引，则该索引仅出现在key列表中`

- #### key_len:表示索引中使用的字节数
`表示索引中使用的字节数，可通过该列计算查询中使用的索引长度。在不损失精确性的情况下，长度越短越好。key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的。`
 
- #### ref: 
`显示索引的哪一列被使用了，如果可能的话，是一个常数。哪些列表或常量被用于查找索引列上的值`

- #### rows: 显示此查询一共扫描了多少行. 这个是一个估计值.

- #### filtered: 表示此查询条件所过滤的数据的百分比

- #### extra: 额外的信息
![](https://mrliuqh.github.io/directionsImg/mysql/explan-extra.png)
- ### Explain的作用
- 表的读取顺序
- 数据读取操作的操作类型
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询

