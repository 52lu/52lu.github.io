---
title: MyISAM和InnoDB之存储结构对比
date: 2019-04-03 19:25:27
tags: 
 - mysql
categories:
 - 数据库 
---


# 1. 什么是存储引擎
<font color=red>MySQL数据库区别于其他数据库的最重要的一个特点就是其插件式的表存储引擎。</font> MySQL插件式的存储引擎架构提供了一系列标准的管理和服务支持，这些标准与存储引擎本身无关，可能是每个数据库系统本身都必需的，如SQL分析器和优化器等，而存储引擎是底层物理结构的实现，每个存储引擎开发者可以按照自己的意愿来进行开发。

存储引擎是MySQL区别于其他数据库的一个最重要特性。存储引擎的好处是，每个存储引擎都有各自的特点，能够根据具体的应用建立不同存储引擎表。

<font color=orange>注意: **需要特别注意的是，存储引擎是基于表的，而不是数据库。**</font>


# 2. MyISAM 

MyISAM存储引擎不支持事务、表锁设计，支持全文索引，主要面向一些在线分析处理(OLAP)数据库应用。
在MySQL 5.5.8版本之前MyISAM存储引擎是默认的存储引擎（除Windows版本外）。数据库系统与文件系统很大的一个不同之处在于对事务的支持，然而MyISAM存储引擎是不支持事务的。
<span style='border-bottom:2px solid orange'>此外，MyISAM存储引擎的另一个与众不同的地方是: 只缓存其索引文件，数据文件的缓存交由操作系统本身来完成，这与其他使用LRU算法缓存数据的大部分数据库大不相同。</span>



## 2.1 存储结构
每个MyISAM在磁盘上存储成3个文件，其文件名都和表名相同，但扩展名分别是: 
- .frm (存储表定义);
- .MYD (MYData，存储数据);
- .MYI (MYIndex，存储索引)。

<span style=background:yellow>数据文件和索引文件可以放置在不同的目录，平均分布 IO，获得更快的速度。</span>

## 2.2 存储空间
**1.可被压缩:**
可以通过使用myisampack工具来进一步压缩数据文件，因为myisampack工具使用赫夫曼（Huffman）编码静态算法来压缩数据，因此使用myisampack工具压缩后的表是只读的，也可以通过myisampack来解压数据文件。

**2.存储大小:**
- MySQL 5.0版本之前: MyISAM默认支持的表大小为4GB，如果需要支持大于4GB的MyISAM表时，则需要制定MAX_ROWS和AVG_ROW_LENGTH属性。
- MySQL 5.0版本开始: MyISAM默认支持256TB的单表数据

## 2.3 存储格式
MyISAM的表支持3种不同的存储格式，分别是：静态（固定长度）表、动态表、压缩表。

### 静态表:

静态表是默认的存储格式。<span style="border-bottom:2px solid orange;">静态表的数据在存储时会按照列的宽度定义补足空格，但是在应用访问的时候并不会得到这些空格，这些空格在返回给应用之前已经去掉。**如果需要保存的内容后面本来就带有空格，那么在返回结果的时候也会被去掉**。</span>

**优缺点:**

静态表中的字段都是非变长字段，这样每个记录都是固定长度的，这种存储方式的优缺点如下:
- 优点: 存储非常迅速，容易缓存，出现故障容易恢复；
- 缺点: 占用的空间通常比动态表多。

### 动态表:
动态表中包含变长字段，记录不是固定长度的。

**优缺点:**
- 优点: 占用的空间相对较少
- 缺点: 频繁地更新和删除记录会产生碎片，需要定期执行OPTIMIZE TABLE语句或myisamchk-r命令来改善性能，并且在出现故障时恢复相对比较困难

### 压缩表
压缩表由myisampack工具创建，占据非常小的磁盘空间。因为每个记录是被单独压缩的，所以只有非常小的访问开支。使用myisampack工具压缩后的表是只读的。

## 2.4 主要特征
- 不支持事物
- 不支持行级锁
- 不支持外键
- 支持全文索引
- 锁机制是:表锁
- 数据可被压缩


# 3.InnoDB
InnoDB存储引擎支持事务，其设计目标主要面向在线事务处理（OLTP）的应用。<font color=red>其特点是行锁设计、支持外键，并支持类似于Oracle的非锁定读，即默认读取操作不会产生锁。</font>从MySQL数据库5.5.8版本开始，InnoDB存储引擎是默认的存储引擎。


## 3.1 存储结构

InnoDB存储引擎的逻辑存储结构和Oracle大致相同，所有数据都被逻辑地存放在一个空间中，我们称之为表空间（tablespace）。<span style="border-bottom:2px solid orange">表空间又由段（segment）、区（extent）、页（page）组成。</font>页在一些文档中有时也称为块（block）。

![](https://52lu.github.io/images/mysql/innodb-struct.png)


### 表空间
<font color=red>表空间可以看做是InnoDB存储引擎逻辑结构的最高层，所有的数据都存放在表空间中。</font>在默认情况下InnoDB存储引擎有一个共享表空间ibdata1，即所有数据都存放在这个表空间内。如果启用了参数innodb_file_per_table，则每张表内的数据可以单独放到一个表空间内。

**<font color=orange>@注意: </font>**
<font color=blue>**如果启用了innodb_file_per_table的参数，需要注意的是每张表的表空间内存放的只是数据、索引和插入缓冲页。其他的数据，如回滚（undo）信息，插入缓冲索引页、系统事务信息，二次写缓冲（Double write buffer）等还是存放在原来的共享表空间内。这同时也说明了另一个问题：即使在启用了参数innodb_file_per_table之后，共享表空间还是会不断地增加其大小**</font>

### 段
<span style="border-bottom:2px solid orange;">表空间是由各个段组成的，常见的段有数据段、索引段、回滚段等。</span>InnoDB存储引擎表是索引组织的（index organized），因此数据即索引，索引即数据。

**结合图1 InnoDB逻辑存储结构说明段:**

- 数据段: 为B+树的叶子节点（图1中的Leaf node segment）。
- 索引段: 为B+树的非索引节点（图1中的Non-leaf node segment）。
- 回滚段: 图1中的Rollback segment

在InnoDB存储引擎中，对段的管理都是由引擎自身所完成，DBA不能也没有必要对其进行控制。这和Oracle数据库中的自动段空间管理（ASSM）类似，从一定程度上简化了DBA对于段的管理。

### 区
区是由连续页组成的空间，在任何情况下每个区的大小都为1MB。为了保证区中页的连续性，InnoDB存储引擎一次从磁盘申请4～5个区。在默认情况下，InnoDB存储引擎页的大小为16KB，即一个区中一共有64个连续的页。

InnoDB 1.2.x版本新增了参数innodb_page_size，通过该参数可以将默认页的大小设置为4K、8K，但是页中的数据库不是压缩。这时区中页的数量同样也为256、128。总之，不论页的大小怎么变化，区的大小总是为1M。

`这里还有这样一个问题：在用户启用了参数innodb_file_per_talbe后，创建的表默认大小是96KB。区中是64个连续的页，创建的表的大小至少是1MB才对啊？其实这是因为在每个段开始时，先用32个页大小的碎片页（fragment page）来存放数据，在使用完这些页之后才是64个连续页的申请。这样做的目的是，对于一些小表，或者是undo这类的段，可以在开始时申请较少的空间，节省磁盘容量的开销。`


### 页

同大多数数据库一样，InnoDB有页（Page）的概念（也可以称为块），<span style="border-bottom:2px solid red;">页是InnoDB磁盘管理的最小单位。</span>在InnoDB存储引擎中，默认每个页的大小为16KB。而从InnoDB 1.2.x版本开始，**可以通过参数innodb_page_size将页的大小设置为4K、8K、16K。若设置完成，则所有表中页的大小都为innodb_page_size，不可以对其再次进行修改。除非通过mysqldump导入和导出操作来产生新的库。**

在InnoDB存储引擎中，常见的页类型有:

- 数据页（B-tree Node）
- undo页（undo Log Page）
- 系统页（System Page）
- 事务数据页（Transaction system Page）
- 插入缓冲位图页（Insert Buffer Bitmap）
- 插入缓冲空闲列表页（Insert Buffer Free List）
- 未压缩的二进制大对象页（Uncompressed BLOB Page）
- 压缩的二进制大对象页（compressed BLOB Page）

### 行
InnoDB存储引擎是面向列的（row-oriented），也就说数据是按行进行存放的。每个页存放的行记录也是有硬性定义的，最多允许存放16KB/2-200行的记录，即7992行记录。

## 3.2 InnoDB 行记录格式
InnoDB存储引擎和大多数数据库一样（如Oracle和Microsoft SQL Server数据库），记录是以行的形式存储的。这意味着页中保存着表中一行行的数据。

### 行格式分类
- InnoDB 1.0.x版本之前: 提供了 Compact 和 Redundant 两种格式来存放行记录数据
- InnoDB 1.0.x版本之后，引入了新的两种行记录格式：Compressed 和 Dynamic。

可以通过命令`show table status  like 'table_name'` 查看当前表使用的行格式，如下:

```$xslt
mysql> show table status like 'hb_user'\G;
*************************** 1. row ***************************
           Name: hb_user
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 398
 Avg_row_length: 288
    Data_length: 114688
Max_data_length: 0
   Index_length: 131072
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2019-10-27 04:00:47
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options:
        Comment: 用户表
1 row in set (0.00 sec)

ERROR:
No query specified
```

#### Compact

Compact行记录是在MySQL 5.0中引入的，其设计目标是高效地存储数据。<font color=red>简单来说，一个页中存放的行数据越多，其性能就越高。</font>

Compact行记录的存储方式如下:
![](https://52lu.github.io/images/mysql/innodb-row-compact.png)

Compact记录头信息:
![](https://52lu.github.io/images/mysql/innodb-compact-header.png)


**Compact特点整理:**

- 首部: Compact行记录格式的首部是一个非NULL变长字段长度列表；
- 变长字段长度:若列的长度小于255字节，用1字节表示;若大于255个字节，用2字节表示。(变长字段的长度最大不可以超过2字节)
- NULL标志位:该位指示了该行数据中是否有NULL值，有则用1表示。该部分所占的字节应该为1字节
- <font color=red>不管是CHAR类型还是VARCHAR类型，NULL在Compact存储格式下都不占用存储空间。
- 记录头信息: 固定占用5字节（40位）
- 列部分就是实际存储每个列的数据
- <font color=red>每行数据除了用户定义的列外，还有两个隐藏列，事务ID列和回滚指针列，分别为6字节和7字节的大小
- InnoDB表没有定义主键，每行还会增加一个6字节的rowid列

#### Redundant
Redundant是MySQL 5.0版本之前InnoDB的行记录存储方式，MySQL 5.0支持Redundant是为了兼容之前版本的页格式。

Redundant行记录的存储方式如下:
![](https://52lu.github.io/images/mysql/innodb-redundant.png)

Redundant记录头信息:
![](https://52lu.github.io/images/mysql/innodb-redundant-header.png)

**Redundant特点整理:**

- 首部 : Redundant行记录格式的首部是一个字段长度偏移列表。
- 字段长度偏移列表 : 若列的长度小于255字节，用1字节表示；若大于255字节，用2字节表示
- 记录头 : Redundant行记录格式的记录头占用6字节（48位）
- NULL: <font color=red>对于VARCHAR类型的NULL值同样不占用存储空间，但是CHAR类型的NULL值会占用存储空间。


#### Compressed 和 Dynamic
- 对于存放在 BLOB 中的数据，完全采用行溢出数据存放
- Compressed 存放的行数据会采用 zlib 算法进行压缩



### 查看表行格式
```$xslt
# 查看当前版本
mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.18    |
+-----------+
1 row in set (0.00 sec)

# 查看用户表使用的行格式
mysql> show table status like 'hb_user'\G;
*************************** 1. row ***************************
           Name: hb_user
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 398
 Avg_row_length: 288
    Data_length: 114688
Max_data_length: 0
   Index_length: 131072
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2019-10-27 04:00:47
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_general_ci
       Checksum: NULL
 Create_options:
        Comment: 用户账号表
1 row in set (0.06 sec)
```
<font color=red>其中row_format属性表示当前所使用的行记录结构类型

### 指定表行格式
```bash
create Table test (
    t1 varchar(10),
    t2 varchar(10)
) engine=INNODB ROW_FORMAT=COMPACT
```