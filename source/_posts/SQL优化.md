---
title: SQL优化
date: 2019-04-15 19:43:22
tags:
 - mysql
categories:
 - 数据库
---

## 1 优化步骤
### 1.1 使用show status 查看各种SQL的执行频率
`show [session|global] status 可以根据需要加上参数"session" 或者"global" 来显示 session 级(当前连接)的统计结果和 global 级(自数据库上次启动至今)的统计结果。如果不写，默认使 用参数是"session"。`


#### 1.1.1 查看CRUD执行情况
``` 
# 查看自数据库上次启动至今的统计结果
mysql> show global status like 'Com_%';
+---------------------------+-------+
| Variable_name             | Value |
+---------------------------+-------+
....
| Com_insert                | 8877  |
| Com_select                | 438   |
| Com_update                | 4138  |
| Com_delete                | 126   |
....
+---------------------------+-------+
142 rows in set (0.00 sec)

```
<font color=red> Com_xxx 表示每个 xxx 语句执行的次数,通常比较关心的是以上几个统计参数</font>

- Com_select:执行 select 操作的次数，一次查询只累加 1。
- Com_insert:执行 INSERT 操作的次数，对于批量插入的 INSERT 操作，只累加一次。
- Com_update:执行 UPDATE 操作的次数。
- Com_update:执行 UPDATE 操作的次数。

> 上面这些参数对于所有存储引擎的表操作都会进行累计。下面这几个参数只是针对 InnoDB 存储引擎的，累加的算法也略有不同。

> mysql> show global status like 'Innodb_rows_%';

- Innodb_rows_read:select 查询返回的行数。
- Innodb_rows_inserted:执行 INSERT 操作插入的行数。
- Innodb_rows_updated:执行 UPDATE 操作更新的行数。
- Innodb_rows_deleted:执行 DELETE 操作删除的行数。


> <font color=orange> 通过以上几个参数，可以很容易地了解当前数据库的应用是以插入更新为主还是以查询操作为主 </font>


#### 1.1.2 查看事务的执行情况
`对于事务型的应用，通过 Com_commit 和 Com_rollback 可以了解事务提交和回滚的情况， 对于回滚操作非常频繁的数据库，可能意味着应用编写存在问题`

- Com_commit: 事务提交的次数
- Com_rollback: 事务回滚的次数


#### 1.1.3 其他参数
- Connections:试图连接 MySQL 服务器的次数
- Uptime:服务器工作时间
- Slow_queries:慢查询的次数



### 1.2 定位执行效率较低的 SQL 语句

#### 1.2.1 慢日志
- 慢查询日志相关参数

```
slow_query_log：是否开启慢查询日志，1表示开启，0表示关闭。

log_slow_queries  ：旧版（5.6以下版本）MySQL数据库慢查询日志存储路径。可以不设置该参数，系统则会默认给一个缺省的文件host_name-slow.log

slow_query_log_file：新版（5.6及以上版本）MySQL数据库慢查询日志存储路径。可以不设置该参数，系统则会默认给一个缺省的文件host_name-slow.log

long_query_time ：慢查询阈值，当查询时间多于设定的阈值时，记录日志。默认10s

log_queries_not_using_indexes：未使用索引的查询也被记录到慢查询日志中（可选项）。

log_output：日志存储方式。log_output='FILE'表示将日志存入文件，默认值是'FILE'。log_output='TABLE'表示将日志存入数据库，这样日志信息就会被写入到mysql.slow_log表中。MySQL数据库支持同时两种日志存储方式，配置的时候以逗号隔开即可，如：log_output='FILE,TABLE'。日志记录到系统的专用日志表中，要比记录到文件耗费更多的系统资源，因此对于需要启用慢查询日志，又需要能够获得更高的系统性能，那么建议优先记录到文件。
```


### 1.3 explan分析SQL
[查看explan使用分析](https://52lu.github.io/2019/06/03/mysql-explan/)


## 2 索引使用
`MySQL 中索引的存储类型目前只有两种(BTREE 和 HASH)，具体和表的存储引擎相关: MyISAM 和 InnoDB 存储引擎都只支持 BTREE 索引;MEMORY/HEAP 存储引擎可以支持 HASH 和 BTREE 索引`

### 2.1 索引类型

#### 2.1.1 普通索引

```
-- 创建索引
CREATE INDEX index_name ON table(column(length))

-- 修改表结构的方式添加索引
ALTER TABLE table_name ADD INDEX index_name ON (column(length))

-- 删除索引
DROP INDEX index_name ON table
```

#### 2.1.2 唯一索引

```
-- 创建索引
REATE UNIQUE INDEX indexName ON table(column(length))

-- 修改表结构的方式添加索引
ALTER TABLE table_name ADD UNIQUE indexName ON (column(length))
```

#### 2.1.3 全文索引（FULLTEXT）

> MySQL允许在char、varchar、text类型上建立全文索引,MySQL5.6 及以后的版本，MyISAM 和 InnoDB 存储引擎均支持全文索引

> [MySQL 之全文索引](https://zhuanlan.zhihu.com/p/35675553)


- MYISAM
```
-- 创建表的适合添加全文索引
CREATE TABLE `table` (
  `id` int(11) NOT NULL AUTO_INCREMENT ,
  `title` char(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL ,
  `content` text CHARACTER SET utf8 COLLATE utf8_general_ci NULL ,
  `time` int(10) NULL DEFAULT NULL ,
  PRIMARY KEY (`id`),
  FULLTEXT (content)
);  
-- 修改表结构添加全文索引
ALTER TABLE table_name ADD FULLTEXT index_content(content)  
-- 直接创建索引
CREATE FULLTEXT INDEX index_content ON table_name(content)
```
- InnoDb

[MySQL中的全文索引（InnoDB存储引擎）](https://www.yangdx.com/2019/03/24.html)

#### 2.1.4 联合索引(多列索引)
```
-- 联合索引创建
create index indexName on table_name（column1，column2，..）
```
`为了说明联合索引的使用，设: key a_b_c (a,b,c) `

##### 在and查询中
```
-- 查询效率最高，索引全覆盖。
select * from test where a=? and b=? and c=?

-- 索引覆盖a和b。
select * from test where a=? and b=?；

-- 经过mysql的查询分析器的优化，索引覆盖a和b。
select * from test where b=? and a=?；

-- 索引覆盖a
select * from test where a=?；。

-- 没有a列，不走索引，索引失效
select * from test where b=? and c=?；。

-- 没有a列，不走索引，索引失效。
select * from test where c=?；
```
> 索引的最左匹配特性

##### 在范围查询中
```
-- 索引覆盖a和b，因b列是范围查询，因此c列不能走索引。
select * from test where a=? and b between ? and ? and c=?；

-- a列走索引，因a列是范围查询，因此b列是无法使用索引。
select * from test where a between ? and ? and b=?；

-- a列走索引，因a列是范围查询，b列是范围查询也不能使用索引。
select * from test where a between ? and ? and b between ? and ? and c=?；
```
> 索引查找遵循最左侧原则。但是遇到范围查询列之后的列索引失效。


##### 在排序中
```
-- a、b、c三列全覆盖索引，查询效率最高。
select * from test where a=? and b=? order by c；

-- a、b列使用索引查找，因b列是范围查询，因此c列不能使用索引，会出现 file sort。
select * from test where a=? and b between ? and ? order by c；
```
##### 如何设置多列索引顺序
`大部分采用经验法则来计算,但是针对特殊情况则不起作用，以下内容是由经验法则推演:`
```
mysql> SELECT
	COUNT( DISTINCT staff_id ) / COUNT( * ) AS staff_id_selectivity,
	COUNT( DISTINCT customer_id ) / COUNT( * ) AS customer_id_selectivity,
	COUNT( * ) 
FROM
	payment;
+-------------------------+----------------------+------------+
| staff_id_selectivity | customer_id_selectivity | COUNT( * ) |
+-------------------------+----------------------+------------+
| 0.0001                  | 0.0373               |      16049 |
+-------------------------+----------------------+------------+
1 row in set (0.12 sec)
```
> customer_id 选择性更高，所以索引顺序应该是:key indexName (customer_id,staff_id)


#### 2.1.5 前缀索引
当要索引的列字符很多时,索引则会很大且变慢。而前缀索引可以只索引列开始的部分字符串,节约索引空间,从而提高索引效率。

##### 前缀索引长度

计算前缀索引长度中的一个办法就是计算完整列的选择性，并使前缀的选择性接近于完整的选择性(1),下面计算显示如何计算完整列的选择性
```
-- desc 字段为 text 
mysql> SELECT
	COUNT( DISTINCT LEFT ( desc, 3 ) ) / count( * ) AS sel3,
	COUNT( DISTINCT LEFT ( desc, 6 ) ) / count( * ) AS sel6,
	COUNT( DISTINCT LEFT ( desc, 9 ) ) / count( * ) AS sel9,
	COUNT( DISTINCT LEFT ( desc, 12 ) ) / count( * ) AS sel12,
	COUNT( DISTINCT LEFT ( desc, 36 ) ) / count( * ) AS sel36 
FROM
	study;
+--------+--------+--------+--------+--------+
| sel3   | sel6   | sel9   | sel12  | sel36  |
+--------+--------+--------+--------+--------+
| 0.1585 | 0.3432 | 0.4091 | 0.4304 | 0.4401 |
+--------+--------+--------+--------+--------+
1 row in set (0.02 sec)

-- 建立前缀索引
alter table study add key (desc(12))
```

`由上述可以看出，当达到前缀长度12，在增加前缀长度，选择性提升的幅度已经很小了。所以可以设置key的长度为12`


##### 前缀索引优点

- 前缀索引是一种能使索引更小、更快的有效办法;

##### 前缀索引缺点
- 无法使用前缀索引做order by 和 group by,
- 无法使用前缀索引覆盖扫描



### 2.2 使用到索引的场景
- 对于联合索引，只要查询的条件中用到了最左边的列，索引一般才会被使用（最左原则）。

- 对于使用 like 的查询，使用【n%】能用到索引，而【%n】不能用到索引。
- 对于大的文本进行搜索，使用全文索引而不用使用 like ‘%...%’
- 对于column_name是索引，使用column_name is null将使用索引。

### 2.3 存在索引但不使用索引
- 如果 MySQL 估计使用索引比全表扫描更慢，则不使用索引

- 如果使用 MEMORY/HEAP(存储引擎) 表并且 where 条件中不使用“=”进行索引列，那么 不会用到索引。heap 表只有在“=”的条件下才会使用索引
- 如果用 or 分割开的条件，如: A or B, 若A、B 中有一个没有索引，则不会使用到索引

- 如果是联合索引，在没有用到最左边列情况下，则不会使用索引。

- 如果使用 like ,是以%开始【%n】不能用到索引
- 如果列类型是字符串，那么查询时需要用把字符常量值用引号引起来，否则的话即便这个列上有索引,也不会使用。

```
-- 假如 number 字段有索引，并且是字符串字段

-- 会使用到索引
select * FROM study WHERE number= '12'
-- 不会使用到索引
select * FROM study WHERE number= 12
```

- 如果where条件中使用表达式则也不会用到索引


```
-- 不会使用到索引
select * from test where id + 1 =7  

-- 会使用到索引
select * from test where id=6  
```
### 2.4 查看索引使用情况

```
mysql> show global status like 'handler_read%';
+-----------------------+------------+
| Variable_name         | Value      |
+-----------------------+------------+
| Handler_read_first    | 2147391    |
| Handler_read_key      | 448430738  |
| Handler_read_last     | 14857      |
| Handler_read_next     | 1056940046 |
| Handler_read_prev     | 1753960    |
| Handler_read_rnd      | 46990892   |
| Handler_read_rnd_next | 239392047  |
+-----------------------+------------+
7 rows in set (0.03 sec)
```

- Handler_read_key:代表了一个行被索引值读的 次数，很低的值表明增加索引得到的性能改善不高，因为索引并不经常使用
- Handler_read_rnd_next:代表在数据文件中读下一行的请求数。如果正进行大量的表扫描，其值较高，则通常说明表索引不正确或写入的查询没有利用索引。（值越高就意味着查询运行低效，应建立索引补救）

[更多内容查看:MySQL Handler 监控](https://jin-yang.github.io/post/mysql-handler.html)


## 3 MySQL优化特定类型的查询
### 3.1 优化关联查询

1. 确保ON或者USING子句中的列上有索引。在创建索引的时候需要考虑到关联的顺序。当表A和表B用列c关联的时候，如果优化器的关联顺序是B、A，那么就不需要在B表的对应列上建索引。除非有其他理由，否则只需要在关联顺序的第二个表的对应列上创建索引，没有用的索引只会带来额外的负担。

2. 确保任何的GROUP BY 和 ORDER BY 中的表达式只涉及一个表中的列，这样MySQL才有可能使用索引来优化这个过程。

3. 当升级MySQL的时候需要注意：关联语法、运算符优先级等其他可能会发生变化的地方。因为以前是普通关联的地方可能会变成笛卡尔积，不同类型的关联可能会生成不同的结果等。

### 3.2 优化INSERT语句

- 如果同时从同一客户插入很多行，尽量使用多个值表的 INSERT 语句，这种方式将大大
缩减客户端与数据库之间的连接、关闭等消耗，使得效率比分开执行的单个 INSERT 语 句快(在一些情况中几倍)。
下面是一次插入多值的一个例子:
```
insert into test values(1,2),(1,3),(1,4)...
```

- 如果从不同客户插入很多行，能通过使用 INSERT DELAYED 语句得到更高的速度。 DELAYED 的含义是让 INSERT 语句马上执行，其实数据都被放在内存的队列中，并没有 真正写入磁盘，这比每条语句分别插入要快的多;LOW_PRIORITY 刚好相反，在所有其 他用户对表的读写完后才进行插入。

- 将索引文件和数据文件分在不同的磁盘上存放(利用建表中的选项)。
- 如果进行批量插入，可以增加 bulk_insert_buffer_size 变量值的方法来提高速度，但是，这只能对 MyISAM 表使用。
- 当从一个文本文件装载一个表时，使用 LOAD DATA INFILE。这通常比使用很多 INSERT 语句快 20 倍。

### 3.3 优化GROUP BY 语句
默认情况下，MySQL 对所有 GROUP BY col1，col2....的字段进行排序。这与在查询中指定 ORDER BY col1，col2...类似。因此，如果显式包括一个包含相同的列的 ORDER BY 子句，则 对 MySQL 的实际执行性能没有什么影响。

如果查询包括 GROUP BY 但用户想要避免排序结果的消耗，则可以指定 ORDER BY NULL 禁止排序，
如下面的例子:

```
-- 未指定 ORDER BY NULL 
mysql> EXPLAIN SELECT tfid,tfMedicalNum from ins_treatment_file GROUP BY tfName;
+----+-------------+--------------------+------+---------------+------+---------+------+-------+---------------------------------+
| id | select_type | table              | type | possible_keys | key  | key_len | ref  | rows  | Extra                           |
+----+-------------+--------------------+------+---------------+------+---------+------+-------+---------------------------------+
|  1 | SIMPLE      | ins_treatment_file | ALL  | NULL          | NULL | NULL    | NULL | 12133 | Using temporary; Using filesort |
+----+-------------+--------------------+------+---------------+------+---------+------+-------+---------------------------------+
1 row in set (0.01 sec)

-- 指定 ORDER BY NULL  
mysql> EXPLAIN SELECT tfid,tfMedicalNum from ins_treatment_file GROUP BY tfName ORDER BY NULL;
+----+-------------+--------------------+------+---------------+------+---------+------+-------+-----------------+
| id | select_type | table              | type | possible_keys | key  | key_len | ref  | rows  | Extra           |
+----+-------------+--------------------+------+---------------+------+---------+------+-------+-----------------+
|  1 | SIMPLE      | ins_treatment_file | ALL  | NULL          | NULL | NULL    | NULL | 12133 | Using temporary |
+----+-------------+--------------------+------+---------------+------+---------+------+-------+-----------------+
1 row in set (0.02 sec)
```
> 从上面的例子可以看出第一个 SQL 语句需要进行“filesort”，而第二个 SQL 由于 ORDER BY NULL 不需要进行“filesort”，而 filesort 往往非常耗费时间


### 3.4 优化ORDER BY 语句
在某些情况中，MySQL 可以使用一个索引来满足 ORDER BY 子句，而不需要额外的排序。

- WHERE 条件和 ORDER BY 使用相同的索引
- ORDER BY 的顺序和索引顺序相同(联合索引)，
- ORDER BY 的字段都是升序或者都是降序。

```
-- 可以使用索引Sql
SELECT * FROM t1 ORDER BY key_part1,key_part2,... ;
SELECT * FROM t1 WHERE key_part1=1 ORDER BY key_part1 DESC, key_part2 DESC; SELECT * FROM t1 ORDER BY key_part1 DESC, key_part2 DESC;

-- 以下几种情况下则不使用索引

-- 原因:order by 的字段混合 ASC 和 DESC
SELECT * FROM t1 ORDER BY key_part1 DESC, key_part2 ASC; 

-- 原因:用于查询行的关键字与 ORDER BY 中所使用的不相同
SELECT * FROM t1 WHERE key2=constant ORDER BY key1;

-- 原因: 对不同的关键字使用 ORDER BY
SELECT * FROM t1 ORDER BY key1, key2; 
```

### 3.5 优化LIMIT 分页
在系统中需要进行分页操作的时候，我们通常会使用LIMIT 加上偏移量的方法实现，同时加上合适的ORDER BY 子句。如果有对应的索引，通常效率会不错，否则，MySQL需要做大量的文件排序操作。

但是有一个非常常见又头疼的问题就是，在偏移量非常大的时候，例如可能是 LIMIT 10000,20这样的查询，此时MySQL需要查询10020 条记录并只返回最后的20条，这样的代价非常高。

如果所有的页面被访问的频率都相同，那么这样的查询平均需要访问半个表的数据。要优化这种查询，要么是在页面中限制分页的数量，要么是优化大偏移量的性能。

优化此类分页查询的一个最简单的办法就是尽可能地使用索引覆盖扫描，而不是查询所有的列。然后根据需要做一次关联操作最后返回所需要的列。对于偏移量很大的时候，这样做的效率会提高很多。考虑下面这个查询：

```
SELECT file_id, description FROM sakila.film ORDER BY title LIMTI 50, 5;
```
如果这个表很大，最好可以将这个查询修改成下面这个样子：
```
SELECT
	flim.file_id,
	flim.description FROM sakila.film
	INNER JOIN (
      SELECT film_id FROM sakila.film ORDER BY title LIMIT 50,5 
	) AS flim USING(film_id)
```

这里的“延迟关联”将大大提高查询效率，它让MySQL扫描尽可能少的页面，获取需要访问的记录后再根据关联列回原表查询所需要的所有列。这个技术也可以用于优化关联查询中的LIMIT子句。

`还有另外一种优化的方法，就是使用应用程序记录上次数据的位置，在下次查询时就可以直接从该记录的位置开始扫描，这样就避免了使用OFFSET。使用这样的方法可以将其变成一个范围查询，无论翻页到多后面，其性能都会很好。`

### 3.6 优化 OR 条件
对于含有 OR 的查询子句，如果要利用索引，则 OR 之间的每个条件列都必须用到索引; 如果没有索引，则应该考虑增加索引
> 如: A or B, 若A、B 中有一个没有索引，则不会使用到索引