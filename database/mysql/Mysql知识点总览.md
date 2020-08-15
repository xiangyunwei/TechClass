## 关系型数据库

关系型数据库，是指采用了关系模型来组织数据的数据库，其以行和列的形式存储数据，以便于用户理解，关系型数据库这一系列的行和列被称为表，一组表组成了数据库。 用户通过查询来检索数据库中的数据，而查询是一个用于限定数据库中某些区域的执行代码。

简单来说，关系模式就是二维表格模型。

### 关系型数据库优势

关系型数据库的优势：

- 易于理解

  关系型二维表的结构非常贴近现实世界，二维表格，容易理解。

- 支持复杂查询
  可以用 SQL 语句方便的在一个表以及多个表之间做非常复杂的数据查询。

- 支持事务
  可靠的处理事务并且保持事务的完整性，使得对于安全性能很高的数据访问要求得以实现。



## MySQL关系型数据库

### 什么是SQL

结构化查询语言 (Structured Query Language) 简称SQL，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统。

### 什么是MySQL

MySQL 是一个关系型数据库管理系统，MySQL 是最流行的关系型数据库管理系统之一，常见的关系型数据库还有 Oracle 、SQL Server、Access 等等。

MySQL在过去由于性能高、成本低、可靠性好，已经成为最流行的开源数据库，因此被广泛地应用在 Internet 上的中小型网站中。

### MySQL 和 MariaDB 的有趣故事

MySQL 最初由瑞典 MySQL AB 公司开发，MySQL 的创始人是乌尔夫·米卡埃尔·维德纽斯，常用昵称蒙提（Monty）。

在被甲骨文公司收购后，现在属于甲骨文公司（Oracle） 旗下产品。Oracle 大幅调涨MySQL商业版的售价，因此导致自由软件社区们对于Oracle是否还会持续支持MySQL社区版有所隐忧。

MySQL 的创始人就是之前那个叫 Monty 的大佬以 MySQL为基础成立分支计划 MariaDB。

MariaDB打算保持与MySQL的高度兼容性，确保具有库二进制奇偶校验的直接替换功能，以及与MySQL API 应用程序接口)和命令的精确匹配。而原先一些使用 MySQL 的开源软件逐渐转向 MariaDB 或其它的数据库。

所以如果看到你公司用的是 MariaDB 不用怀疑，其实它骨子里还是 MySQL，学会了MySQL 也就会了 MariaDB。 

一个彩蛋：MariaDB 是以 Monty 的小女儿Maria命名的，就像MySQL是以他另一个女儿 My 命名的一样，两款鼎鼎大名的数据库分别用两个女儿的名字命名，你大爷还是你大爷，老爷子牛皮 :smile::cow::cow::cow:

![20170531-DSC06942.jpg](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7b/20170531-DSC06942.jpg/800px-20170531-DSC06942.jpg)



### 如何查看MySQL当前版本号？

在系统命令行下：`mysql -V`

连接上MySQL命令行输入: 

`> status`;

``` shell
Server:			MySQL
Server version:		5.5.45-cdb20180926-log Source distribution
Protocol version:	10
```

或 ` select version();`

```shell
+------------------------+
| version()              |
+------------------------+
| 5.5.45-xxxxx-log |
+------------------------+
```





## 基础数据类型

整数类型：BIT、BOOL、TINY INT、SMALL INT、MEDIUM INT、 INT、 BIG INT

浮点数类型：FLOAT、DOUBLE、DECIMAL

字符串类型：CHAR、VARCHAR、TINY TEXT、TEXT、MEDIUM TEXT、LONGTEXT、TINY BLOB、BLOB、MEDIUM BLOB、LONG BLOB

日期类型：Date、DateTime、TimeStamp、Time、Year

其他数据类型：BINARY、VARBINARY、ENUM、SET、Geometry、Point、MultiPoint、LineString、MultiLineString、Polygon、GeometryCollection等。



## MySQL事务

事务就是「一组原子性的SQL查询」，或者说一个独立的工作单元。如果数据库引擎能够成功地对数据库应用该组查询的全部语句，那么就执行该组查询。如果其中有任何一条语句因为崩溃或其他原因无法执行，那么所有的语句都不会执行。也就是说，事务内的语句，要么全部执行成功，要么全部执行失败。

事务这块我之前写过一篇文章来分析，更细致的了解，点这个传送门：[面试官：你说对MySQL事务很熟，那我问你十个问题](//mp.weixin.qq.com/s?__biz=MzIwMjM4NDE1Nw==&mid=2247483807&idx=1&sn=760d3b36c3742b0413b7f6f095506fd5&chksm=96de37eda1a9befb371bf03653df7528921262c95ab1483b4ecb86a25a8315a9598c723adaa5&token=969827033&lang=zh_CN#rd)

### 事务例子

这里举个栗子，转账的操作可以简化抽成一个事务，包含如下步骤：

1. 查询CMBC账户的余额是否大于100万
2. 从CMBC账户余额中减去100万
3. 在ICBC账户余额中增加100万 

以下语句对应创建了一个转账事务：

```sql
START TRANSACTION;
SELECT balance FROM CMBC WHERE username='lemon';
UPDATE CMBC SET balance = balance - 1000000.00 WHERE username = 'lemon';
UPDATE CMBC SET balance = balance + 1000000.00 WHERE username = 'lemon';
COMMIT;
```

### 事务ACID

ACID 其实是事务特性的英文首字母缩写，具体的含义是这样的：

- 原子性（atomicity)
  一个事务必须被视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作。
- 一致性（consistency)
  数据库总是从一个一致性的状态转换到另外一个一致性的状态。在前面的例子中，一致性确保了，即使在执行第三、四条语句之间时系统崩溃，CMBC账户中也不会损失100万，不然 lemon要哭死，因为事务最终没有提交，所以事务中所做的修改也不会保存到数据库中。
- 隔离性（isolation)
  通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的。在前面的例子中，当执行完第三条语句、第四条语句还未开始时，此时如果有其他人准备给 lemon 的 CMBC 账户存钱，那他看到的CMBC账户里还是有100万的。
- 持久性（durability)
  一旦事务提交，则其所做的修改就会永久保存到数据库中。此时即使系统崩溃，修改的数据也不会丢失。持久性是个有点模糊的概念，因为实际上持久性也分很多不同的级别。有些持久性策略能够提供非常强的安全保障，而有些则未必。而且「不可能有能做到100%的持久性保证的策略」否则还需要备份做什么。

![ACID](https://github.com/lemonchann/images/raw/master/database/mysql/mysql_shi_wu/ACID.png)



### 事务是如何通过日志来实现

Innode存储引擎的事务是通过事务日志（Transaction log）来实现原子性和持久性。

1. 事务开始时，会记录该事务的日志序号 LSN (log sequence number) ，在InnoDB存储引擎中，LSN占用8个字节，并且单调递增。

2. 事务执行时，会往 InnoDB 存储引擎**内存**中的日志缓存里面插入事务日志；

3. 事务提交时，必须将存储引擎的日志缓冲写入**磁盘**中，也就是写数据前，需要先写日志。

而事务日志是通过回滚日志（undo log）和重做日志（redo log）以及存储引擎日志缓冲（Innodb log buffer）来实现，前者用于对事务的影响进行撤销，后者在错误处理时对已经提交的事务进行重做，它们能保证两点：

1. 发生错误或者需要回滚的事务能够成功回滚（原子性）；
2. 在事务提交后，数据没来得及写会磁盘就宕机时，在下次重新启动后能够成功恢复数据（持久性）；

undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行回滚时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。



## 存储引擎

### MySQL存储引擎类型有哪些？

最常用的存储引擎是InnoDB引擎和MyISAM存储引擎，InnoDB是MySQL的默认事务引擎。

查看数据库表当前支持的引擎 ：

```
show table status from 'your_db_name' where name='your_table_name'; 
查询结果表中的`Engine`字段指示存储引擎类型。
```

### InnoDB存储引擎

#### 应用场景是什么？

InnoDB 是 MySQL的默认「事务引擎」，被设置用来处理大量短期（short-lived）事务，短期事务大部分情况是正常提交的，很少会回滚。

#### 特性有哪些？

采用多版本并发控制（MVCC，MultiVersion Concurrency Control）来支持高并发。并且实现了四个标准的隔离级别，通过间隙锁`next-key locking`策略防止幻读的出现。

引擎的表基于聚簇索引建立，聚簇索引对主键查询有很高的性能。不过它的二级索引`secondary index`非主键索引中必须包含主键列，所以如果主键列很大的话，其他的所有索引都会很大。因此，若表上的索引较多的话，主键应当尽可能的小。另外InnoDB的存储格式是平台独立。

InnoDB做了很多优化，比如：磁盘读取数据方式采用的可预测性预读、自动在内存中创建hash索引以加速读操作的自适应哈希索引（adaptive hash index)，以及能够加速插入操作的插入缓冲区（insert buffer)等。

InnoDB通过一些机制和工具支持真正的热备份，MySQL的其他存储引擎不支持热备份，要获取一致性视图需要停止对所有表的写入，而在读写混合场景中，停止写入可能也意味着停止读取。

### MyISAM存储引擎

#### 应用场景有哪些？

MyISAM是MySQL 5.1及之前的版本的默认的存储引擎。MyISAM提供了大量的特性，包括全文索引、压缩、空间函数（GIS)等，但MyISAM不「支持事务和行级锁」，对于只读数据，或者表比较小、可以容忍修复操作，依然可以使用它。

#### 特性有哪些？

MyISAM「不支持行级锁而是对整张表加锁」。读取时会对需要读到的所有表加共享锁，写入时则对表加排它锁。但在表有读取操作的同时，也可以往表中插入新的记录，这被称为并发插入。

MyISAM表可以手工或者自动执行检查和修复操作。但是和事务恢复以及崩溃恢复不同，可能导致一些「数据丢失」，而且修复操作是非常慢的。

对于MyISAM表，即使是`BLOB`和`TEXT`等长字段，也可以基于其前500个字符创建索引，MyISAM也支持「全文索引」，这是一种基于分词创建的索引，可以支持复杂的查询。

如果指定了` DELAY_KEY_WRITE `选项，在每次修改执行完成时，不会立即将修改的索引数据写入磁盘，而是会写到内存中的键缓冲区，只有在清理键缓冲区或者关闭表的时候才会将对应的索引块写入磁盘。这种方式可以极大的提升写入性能，但是在数据库或者主机崩溃时会造成「索引损坏」，需要执行修复操作。

### MyISAM  与  InnoDB  存储引擎 5 大区别

- InnoDB支持事物，而MyISAM不支持事物
- InnoDB支持行级锁，而MyISAM支持表级锁
- InnoDB支持MVCC, 而MyISAM不支持
- InnoDB支持外键，而MyISAM不支持
- InnoDB不支持全文索引，而MyISAM支持。

一张表简单罗列两种引擎的主要区别，如下图。

![引擎对比](https://mmbiz.qpic.cn/mmbiz_png/ceNmtYOhbMQs4Ar4C7lr5CFmTkzO5qFP4ziaEN8O2vgic8ibP9RnGibTDcVaAxTKfTxeicpCrYquzWXRstmdviaCrSzA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



### InnoDB  引擎的四大特性

#### 插入缓冲（Insert buffer)

Insert Buffer 用于非聚集索引的插入和更新操作。先判断插入的非聚集索引是否在缓存池中，如果在则直接插入，否则插入到 Insert Buffer 对象里。再以一定的频率进行 Insert Buffer 和辅助索引叶子节点的 merge 操作，将多次插入合并到一个操作中，提高对非聚集索引的插入性能。



#### 二次写 (Double write)

Double Write由两部分组成，一部分是内存中的double write buffer，大小为2MB，另一部分是物理磁盘上共享表空间连续的128个页，大小也为2MB。在对缓冲池的脏页进行刷新时，并不直接写磁盘，而是通过memcpy函数将脏页先复制到内存中的该区域，之后通过doublewrite buffer再分两次，每次1MB顺序地写入共享表空间的物理磁盘上，然后马上调用fsync函数，同步磁盘，避免操作系统缓冲写带来的问题。



#### 自适应哈希索引 (Adaptive Hash Index) 

InnoDB会根据访问的频率和模式，为热点页建立哈希索引，来提高查询效率。索引通过缓存池的 B+ 树页构造而来，因此建立速度很快，InnoDB存储引擎会监控对表上各个索引页的查询，如果观察到建立哈希索引可以带来速度上的提升，则建立哈希索引，所以叫做自适应哈希索引。



#### 缓存池

为了提高数据库的性能，引入缓存池的概念，通过参数 innodb_buffer_pool_size 可以设置缓存池的大小，参数 innodb_buffer_pool_instances 可以设置缓存池的实例个数。缓存池主要用于存储以下内容：

缓冲池中缓存的数据页类型有：索引页、数据页、undo页、插入缓冲 (insert buffer)、自适应哈希索引(adaptive hash index)、InnoDB存储的锁信息 (lock info)和数据字典信息 (data dictionary)。



#####  SELECT COUNT(*) 在哪个引擎执行更快？

 SELECT COUNT(*)  常用于统计表的总行数，**在 MyISAM  存储引擎中执行更快，前提是不能加有任何WHERE条件**。

这是因为MyISAM对于表的行数做了优化，内部用一个变量存储了表的行数，如果查询条件没有WHERE条件则是查询表中一共有多少条数据，MyISAM可以做到迅速返回，所以也解释了如果加WHERE条件，则该优化就不起作用了。

innodb的表也有这么一个存储了表行数的变量，但是这个值是一个估计值，没有什么实际意义。



## 索引

### 什么是Mysql索引？

索引是存储引擎用于快速找到记录的一种数据结构。

举个栗子，索引就像字典的目录，当我们不认识某个字需要查找字典的时候，可以选择整本字典从头到尾翻看查找，不过我相信大部分人都不会这么查字典，小学的我们就学会了字典的正确使用方式，正确的做法是从字典开始的目录按字母或偏旁部首排序查找到想要的页码，然后直接找到页码完成查找。

如你所见，索引的存在作用就是加快了我们查找内容的速度，在数据库中索引也是这个作用。

### MySQl索引有哪些类型？

两大类索引类型：B+ Tree索引 和 Hash 索引。

在MySQL中索引是在存储引擎层而不是在服务器层实现。一般来说谈到MySQL索引大部分情况下是B-Tree索引。



### 如何查看表的索引信息？

**查看索引**：`SHOW INDEX FROM <tablename>; `

**查看建表语句包含的索引信息**：`SHOW CREATE TABLE <tablename>`



### 统计MySQL 库表和索引大小？

通过MySQL的 information_schema 数据库，可查询数据库中每个表占用的空间、表记录的行数；该库中有一个 TABLES 表，字段含义：

```
TABLE_SCHEMA : 数据库名
TABLE_NAME：表名
ENGINE：所使用的存储引擎
TABLES_ROWS：记录数
DATA_LENGTH：数据大小
INDEX_LENGTH：索引大小
```

统计索引容量的SQL命令：

```sql
// 查看当前MySQL服务器所有索引的大小(以MB为单位,默认是字节)
SELECT CONCAT(ROUND(SUM(index_length)/(1024*1024), 2), ' MB') AS 'Total Index Size' FROM TABLES

// 查看某一个库的索引大小
SELECT CONCAT(ROUND(SUM(index_length)/(1024*1024), 2), ' MB') AS 'Total Index Size' FROM TABLES  WHERE table_schema = 'XXX';

// 查看某一个表的索引大小
SELECT CONCAT(ROUND(SUM(index_length)/(1024*1024), 2), ' MB') AS 'Total Index Size' FROM TABLES  WHERE table_schema = 'yyyy' and table_name = "xxxxx";  

// 汇总查看一个库中的数据大小及索引大小
SELECT CONCAT(table_schema,'.',table_name) AS 'Table Name', CONCAT(ROUND(table_rows/1000000,4),'M') AS 'Number of Rows', CONCAT(ROUND(data_length/(1024*1024*1024),4),'G') AS 'Data Size', CONCAT(ROUND(index_length/(1024*1024*1024),4),'G') AS 'Index Size', CONCAT(ROUND((data_length+index_length)/(1024*1024*1024),4),'G') AS'Total'FROM information_schema.TABLES WHERE table_schema LIKE 'xxxxx';

```



### MySql中有哪些锁

MyISAM 支持表锁，InnoDB支持表锁和行锁，默认为行锁。由于MySQL默认的存储引擎是InnoDB 下面详细介绍它的锁类型：

- 表级锁：开销小，加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量最低。
- 行级锁：开销大，加锁慢，会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高。

具体的锁定模式实际上可以分为四种`：`共享锁（S）`，`排他锁（X）`，`意向共享锁（IS`）和`意向排他锁（IX）,其中前两个是行锁，后两个是表锁[^2]

各种锁的兼容模式如下：

![img](https://img-blog.csdn.net/20180823165939881?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pjbF9sb3ZlX3d4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





### 常用的性能优化查询指令？

#### `SHOW GLOBAL STATUS  ` 查询MySQL服务运行状态。[^3]

```sql
# 查看文件打开数(open_files)
mysql> show global status like 'open_files';

# 查看表锁情况
show global status like 'table_locks%';

# 查看线程使用情况
mysql> show global status like 'Thread%';

# 查询缓存
show global status like 'qcache%';

# 查看慢查询设置
mysql> show variables like '%slow%';

# 查看连接数
mysql> show variables like 'max_connections';

# 查看key_buffer_size
mysql> show variables like 'key_buffer_size';

# 查看临时表
mysql> show global status like 'created_tmp%';
```

#### 

#### show variables查看系统变量的值

**语法**

```sql
SHOW [GLOBAL | SESSION] VARIABLES
    [LIKE 'pattern' | WHERE expr]
```

同时可以用通配符`%` 或 `where` 子句做匹配查询，举个栗子：

```
mysql> show variables like 'innodb_thread%'; 
+---------------------------+-------+
| Variable_name             | Value |
+---------------------------+-------+
| innodb_thread_concurrency | 0     |
| innodb_thread_sleep_delay | 10000 |
+---------------------------+-------+

mysql> show variables where variable_name = 'version';
+---------------+------------+
| Variable_name | Value      |
+---------------+------------+
| version       | 5.7.21-log |
+---------------+------------+
```



#### `SHOW PROCESSLIST` 查看 MySQL 用户正在运行的线程。

>  需要注意除了 root 用户能看到所有正在运行的线程外，其他用户都只能看到自己正在运行的线程，看不到其它用户正在运行的线程。

输出示例：

```sql
+----------+--------------+---------------------+------------+---------+------+-------+------------------+
| Id       | User         | Host                | db         | Command | Time | State | Info             |
+----------+--------------+---------------------+------------+---------+------+-------+------------------+
| 15207121 | u_test | 10.11.40.60:6666 | test_db | Query   |    0 | NULL  | SHOW PROCESSLIST |
+----------+--------------+---------------------+------------+---------+------+-------+------------------+

```

参数含义：

- Id             #线程的唯一标识，要kill一个语句的时候很有用，show processlist 显示的信息时来自information_schema.processlist 表，所以这个Id就是这个表的主键
- User         #启动这个线程的用户
- Host          #显示这个连接从哪个ip的哪个端口上发出
- db              #数据库名
- command  # 指此刻该线程正在执行的命令，一般是休眠（sleep），查询（query），连接（connect）
- Time          #连接持续时间，单位是秒
- State         #显示当前sql语句的状态，和 Command 对应
- Info            #一般记录的是线程执行的语句。默认只显示前100个字符，也就是你看到的语句可能是截断了的，要看全部信息，需要使用 show full processlist

**重点关注Command 和 State 参数，代表了当前运行线程的状态**。[^5]



### 数据库设计三范式

1. 1范式：1NF是对属性的原子性约束，要求属性具有原子性，不可再分解；(只要是关系型数据库都满足1NF)
2. 2范式：2NF是对记录的惟一性约束，要求记录有惟一标识，即实体的惟一性；
3. 3范式：3NF是对字段冗余性的约束，即任何字段不能由其他字段派生出来，它要求字段没有冗余。没有冗余的数据库设计可以做到
4. 但是，没有冗余的数据库未必是最好的数据库，有时为了提高运行效率，就必须降低范式标准，适当保留冗余数据。具体做法是：在概念数据模型设计时遵守第三范式，降低范式标准的工作放到物理数据模型设计时考虑。降低范式就是增加字段，允许冗余。



### delete、drop、 truncate 区别在哪

- 当你不再需要该表时， 用 drop;
- 当你仍要保留该表，但要删除所有记录时， 用 truncate
- 当你要删除部分记录时（always with a where clause), 用 delete



### sql语句分类

1. DDL：数据定义语言（create alter drop）
2. DML：数据操作语句（insert update delete） 
3. DTL：数据事务语句（commit collback savapoint）
4. DCL：数据控制语句（grant revoke）



### 什么是MySql视图

视图是虚拟表，并不储存数据，只包含定义时的语句的动态数据。

```sql
create view view_name as sql查询语句
```



### 什么是MySql存储过程

一条或多条sql语句集合，其优点为(浓缩：简单/安全/高性能)：

- 存储过程能实现较快的执行速度
- 存储过程允许标准组件是编程。
- 存储过程可以用流程控制语句编写，有很强的灵活性，可以完成复杂的判断和较复杂的运算。
- 存储过程可被作为一种安全机制来充分利用。
- 存储过程能够减少网络流量

```sql
delimiter 分隔符
create procedure|proc proc_name()
begin
    sql语句
end 分隔符
delimiter ；    --还原分隔符，为了不影响后面的语句的使用
默认的分隔符是；但是为了能在整个存储过程中重用，因此一般需要自定义分隔符（除\外）
show procedure status like ""; --查询存储过程，可以不适用like进行过滤
drop procedure if exists；--删除存储过程
```



#### MySQL存储过程和函数的区别?

存储过程和函数是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程和函数可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。[^ 5]

**相同点**

- 存储过程和函数都是为了可重复的执行操作数据库的 SQL 语句的集合。

- 存储过程和函数都是一次编译后缓存起来，下次使用就直接命中已经编译好的 sql 语句，减少网络交互提高了效率。

**不同点**

- 标识符不同，函数的标识符是 function，存储过程是 procedure。
- 函数返回单个值或者表对象，而存储过程没有返回值，但是可以通过OUT参数返回多个值。
- 函数限制比较多，比如不能用临时表，只能用表变量，一些函数都不可用等，而存储过程的限制相对就比较少。
- 一般来说，存储过程实现的功能要复杂一点，而函数的实现的功能针对性比较强
- 函数的参数只能是 IN 类型，存储过程的参数可以是`IN OUT INOUT`三种类型。
- 存储函数使用 select 调用，存储过程需要使用 call 调用。



### 什么是触发器

在对表数据进行变动的时候进行具体的操作，有六种，分别为增删改的前后操作。[^ 6]

```sql
CREATE TRIGGER trigger_name trigger_time trigger_event ON tbl_name FOR EACH 
ROW trigger_stmt
```

字段解释：

`trigger_name` : 触发器名称，用户自行指定
`trigger_time`： 触发时机，取值**BEFORE**（之前）、**AFTER**（之后）
`trigger_event` : 出发事件，**INSERT**、**UPDATE**、**DELETE**。（插入、更新、删除）
`tbl_name` : 需要建立触发器的表名。
`trigger_stmt` : 触发程序体，可以是一条**SQL**语句或是**BEGIN**和**END**包含的索条语句



### 存储过程和触发器的关系？

1. 触发器可以理解为是一个隐藏的不需要参数，不需要显示调用的存储过程，由于是隐藏的，无形中增加了系统的复杂性和数据库的维护成本。
2. 用嵌套触发器实现复杂的逻辑，很容易出现死锁。所以大部分有经验前辈的建议是摒弃触发器，触发器的功能基本都可以用存储过程来实现。
3. 存储过程显式调用很代码易于理解和阅读，触发器隐式调用容易被忽略。
4. 存储过程也有移植性问题，不能跨库移植，比如事先是在mysql数据库的存储过程，如果要移植到oracle上面，那就需要重写所有的存储过程。

**最后，触发器和存储过程在高并发应用中都不建议使用，其中触发器可以用事务替代，存储过程可以用后端脚本替代**。



### 什么是MySQl分区表 Partition  

当数据库中的数据越来越多，会导致查询和插入数据非常缓慢，为了解决大数据表的这个问题，MySQL 5.1开始支持数据表分区，在逻辑上为一个表，在物理上存储在多个文件中。

查看是否支持分区表，如果现实有`partition` 字段就是支持的。

```
mysql> show plugins 
+--------------------------+--------+--------------------+---------+---------+
| Name                     | Status | Type               | Library | License |
+--------------------------+--------+--------------------+---------+---------+
| partition                | ACTIVE | STORAGE ENGINE     | NULL    | GPL     |
+--------------------------+--------+--------------------+---------+---------+
```

目前MySQL支持范围分区（RANGE），列表分区（LIST），哈希分区（HASH）以及KEY分区四种[^ 7] [^ 8]



### 什么是MySQL视图

>  视图`view`是基于 SQL 语句的结果集的可视化的表，视图是由查询结果形成的一张虚拟表，视图也包含行和列，就像一个真实的表。使用视图查询可以使查询数据相对安全，通过视图可以隐藏一些敏感字段和数据，从而只对用户暴露安全数据。视图查询也更简单高效，如果某个查询结果出现的非常频繁或经常拿这个查询结果来做子查询，将查询定义成视图可以使查询更加便捷。

#### 语法

```
# 创建视图
CREATE VIEW 视图名 AS SELECT 查询语句;

# 修改视图
ALTER VIEW 视图名 AS SELECT 查询语句;

# 删除视图
DROP VIEW 视图名;
```



## MySQl 日志相关

### MySql 有哪些类型日志

MySQL 中主要有六种日志：重做日志（redo log）、回滚日志（undo log）、二进制日志（binlog）、错误日志（errorlog）、慢查询日志（slow query log）、一般查询日志（general log），中继日志（relay log）。[^9] [^10]

- 错误日志：默认开启，记录启动，运行或者停止 mysql 时出现的问题，和事件调度器运行一个事件时产生的信息；
- 查询日志：不要被查询的名字误导，其实查询日志里面记录了数据库执行的所有命令包括INSERT、DELETE、UPDATE，不管语句是否正确，都会被记录。
- 回滚日志：保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读。
- 重做日志：防止在发生故障的时间点，尚有脏页未写入磁盘，在重启mysql服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性。
- 二进制日志： 记录了对MySQL数据库执行更改的所有操作，并且记录了语句发生时间、执行时长、操作数据等其它额外信息，但是它不记录SELECT、SHOW等那些不修改数据的SQL语句。用于主从复制中，从库利用主库上的binlog进行重播，实现主从同步。
- 慢查询日志：慢查询日志记录的慢查询不仅仅是执行比较慢的SELECT语句，还有INSERT，DELETE，UPDATE，CALL等 DML 操作，只要超过了指定时间，都可以称为"慢查询"，被记录到慢查询日志中。默认情况下，慢查询日志是不开启的，需要手动开启。
- 中继日志：中继日志类似二进制日志，用来给 slave 库恢复。是从库服务器 I/O 线程将主库服务器的二进制日志读取过来记录到从库服务器本地文件，然后从库的 SQL 线程会读取 relay-log 日志的内容并应用到从库服务器上。



### MySQL binlog日志格式由几种？

binlog主要有三种格式：  STATEMENT、ROW 和 MIXED 

-  STATEMENT 格式：binlog 记录的是数据库上执行的原生SQL语句。

  **优点**是数据紧凑，节省存储空间，可以通过mysqlbinlog 工具读懂日志中的内容。 

  **缺点**是同一条 SQL 在主库和从库上执行的时间可能稍微或很大不相同，因为在传输的二进制日志中，除了查询语句，还包括了一些元数据信息，如当前的时间戳。

-  ROW 格式：从 MySQL5.1开始 binlog 支持基于行的复制，不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了，这种方式会将实际数据记录在二进制日志中。

  **优点**是可以正确地复制每一行数据，几乎没有 STATEMENT 格式的那些缺点。

  **缺点**是二进制日志可能会很大且不直观，也不能使用mysqlbinlog来查看二进制日志  

- MIXED格式：MIXED也是MySQL默认使用的二进制日志记录方式，但MIXED格式默认采用基于语句的复制，一旦发现基于语句的无法精确的复制时（比如用到UUID()、USER()、CURRENT_USER()、ROW_COUNT()等无法确定的函数），就会采用基于行的复制。

  


### 如何查看和设置日志相关配置

#### 查看日志参数

通过 `show variables like '%log%';` 命令查看日志相关变量值。

```sql
> show variables like '%log%';
+-----------------------------------------+-------------------------------------------------+
| Variable_name                           | Value                                           |
+-----------------------------------------+-------------------------------------------------+
| back_log                                | 50                                              |
| binlog_cache_size                       | 32768                                           |
| binlog_direct_non_transactional_updates | OFF                                             |
| binlog_format                           | ROW                                             |
| binlog_stmt_cache_size                  | 32768                                           |
| expire_logs_days                        | 0                                               |
| general_log                             | OFF                                             |
| general_log_file                        | /data1/mysql_root/data/xxxx.log      |
| innodb_flush_log_at_trx_commit          | 2                                               |
| innodb_locks_unsafe_for_binlog          | OFF                                             |
| innodb_log_buffer_size                  | 67108864                                        |
| innodb_log_file_size                    | 536870912                                       |
| innodb_log_files_in_group               | 2                                               |
| innodb_log_group_home_dir               | /data/mysql_root/log/xxxx                      |
| innodb_mirrored_log_groups              | 1                                               |
| log                                     | OFF                                             |
| log_bin                                 | ON                                              |
| log_bin_trust_function_creators         | ON                                              |
| log_error                               | /data1/mysql_root/data/xxxx/xxxx.site.err |
| log_output                              | FILE                                            |
| log_queries_not_using_indexes           | OFF                                             |
| log_slave_updates                       | ON                                              |
| log_slow_queries                        | ON                                              |
| log_warnings                            | 1                                               |
| max_binlog_cache_size                   | 18446744073709547520                            |
| max_binlog_size                         | 268435456                                       |
| max_binlog_stmt_cache_size              | 18446744073709547520                            |
| max_relay_log_size                      | 0                                               |
| relay_log                               | /data/mysql_root/log/xxxx/relay-bin            |
| relay_log_index                         |                                                 |
| relay_log_info_file                     | relay-log.info                                  |
| relay_log_purge                         | ON                                              |
| relay_log_recovery                      | OFF                                             |
| relay_log_space_limit                   | 0                                               |
| slow_query_log                          | ON                                              |
| slow_query_log_file                     | /data1/mysql_root/data/xxxx/slow_query.log     |
| sql_log_bin                             | ON                                              |
| sql_log_off                             | OFF                                             |
| sync_binlog                             | 0                                               |
| sync_relay_log                          | 0                                               |
| sync_relay_log_info                     | 0                                               |
+-----------------------------------------+-------------------------------------------------
```

#### 设置日志参数

有两种设置方式：

- 修改配置文件 my.cnf，修改完重启 mysql 服务生效。
- mysql 命令行设置，比如前面查出`back_log`参数值 50，这里可以修改设置 `set global back_log=60`



### MySql服务默认端口号是多少 ？

3306

查看命令：`> show variables like 'port';`



## 自增主键相关

### innoDB存储引擎的主键用自增方式还是UUID？

**结论**：为了提高性能 在InnoDB上尽量采用自增字段做主键 。

由于 innoDB 中的主键是聚簇索引，当数据插入时使用B+ 树结构，各条数据按主键顺序排放，使用自增方式生成的主键 ID 是有序的，方便数据插入，减少了插入的磁盘IO消耗。

`UUID`被设计为在空间和时间全球独一无二的数字，UUID 值是一个随机字符串，类似` 887212cf-72fc-11e7-bdf0-f0def1e6678c `这种。由于 UUID 是随机生成唯一值，所以插入数据库时的位置具有不确定性，无序插入会存在许多内存碎片，内存空间的占用量也会比自增主键大，区间查找也没自增主键性能优。



### 无符号整型自增主键达到最大值再插入会怎么样？

无符号整型主键的范围是 `0～4294967295` ，约`43亿` 

达到最大值再插入会报错：`  Duplicate entry '4294967295' for key 'PRIMARY' `



###  VARCHAR(50) 能存放几个UTF8编码的汉字？

与版本相关。

mysql 4.0以下版本，varchar(50) `指的是50字节，如果存放utf8汉字时（每个汉字3字节），只能存放16个

mysql 5.0以上版本，varchar(50), 指的是50字符，无论存放的是数字、字母还是 UTF8 汉字，都可以存放50个。



### int（20）的含义

是指显示字符的长度

但要加参数的，最大为255，比如它是记录行数的id,插入10笔资料，它就显示00000000001 ~~~00000000010，当字符的位数超过11,它也只显示11位，如果你没有加那个让它未满11位就前面加0的参数，它不会在前面加0

20表示最大显示宽度为20，但仍占4字节存储，存储范围不变；



### 获取MySql 版本的指令

###  用  DISTINCT  过滤 多列的规则

### 有哪些通用 SQL 函数

- CONCAT(A, B) - 连接两个字符串值以创建单个字符串输出。通常用于将两个或多个字段合并为一个字段。
- FORMAT(X, D)- 格式化数字X到D有效数字。
- CURRDATE(), CURRTIME()- 返回当前日期或时间。
- NOW（） - 将当前日期和时间作为一个值返回。
- MONTH（），DAY（），YEAR（），WEEK（），WEEKDAY（） - 从日期值中提取给定数据。
- HOUR（），MINUTE（），SECOND（） - 从时间值中提取给定数据。
- DATEDIFF（A，B） - 确定两个日期之间的差异，通常用于计算年龄
- SUBTIMES（A，B） - 确定两次之间的差异。
- FROMDAYS（INT） - 将整数天数转换为日期值。



### MySQL的 主从复制原理以及流程

基本原理流程，3个线程以及之间的关联；

**主**：binlog线程——记录下所有改变了数据库数据的语句，放进master上的binlog中；

**从**：io线程——在使用start slave 之后，负责从master上拉取 binlog 内容，放进 自己的relay log中；

**从**：sql执行线程——执行relay log中的语句；



### explain 显示出的各个字段含义

### MySql备份工具 mysqldump 和 xtranbackup

### innodb的读写参数优化

**读取参数**

```
global buffer pool以及 local buffer；
复制代码
```

**写入参数**

```
innodb_flush_log_at_trx_commit

innodb_buffer_pool_size
复制代码
```

**IO相关的参数**

```
innodb_write_io_threads = 8

innodb_read_io_threads = 8

innodb_thread_concurrency = 0
复制代码
```

**缓存参数以及缓存的适用场景**。

```
query cache/query_cache_type
```





### 查看当前数据库容量的指令？



### Reference

 https://zhuanlan.zhihu.com/p/58698226 

 https://www.jianshu.com/p/5052f6a454ef 

 https://juejin.im/post/5cb6c4ef51882532b70e6ff0 

[并行事务的原子性](https://draveness.me/mysql-transaction/#%E5%B9%B6%E8%A1%8C%E4%BA%8B%E5%8A%A1%E7%9A%84%E5%8E%9F%E5%AD%90%E6%80%A7https://draveness.me/mysql-transaction/#并行事务的原子性 ) 

[InnoDB的关键特性-插入缓存，两次写，自适应hash索引](https://www.cnblogs.com/wade-luffy/p/6279500.html)

[一文了解InnoDB事务实现原理](https://zhuanlan.zhihu.com/p/48327345)

[五分钟了解Mysql的行级锁——《深究Mysql锁》](https://blog.csdn.net/zcl_love_wx/article/details/81983267)

[^1]: [浅入深出MySQL 中事务的实现](https://draveness.me/mysql-transaction/)

[^2]: [五分钟了解Mysql的行级锁——《深究Mysql锁》](https://blog.csdn.net/zcl_love_wx/article/details/81983267)
[^ 3]: [mysql状态分析之show global status](https://blog.csdn.net/qq_26941173/article/details/77823184)
[^4]:  [mysql: show processlist 详解](https://zhuanlan.zhihu.com/p/30743094)
[^5]: [MySQL 存储过程和函数](https://www.jianshu.com/p/5624ef1dc5d3)
[^6]:https://www.jianshu.com/p/6b694637fd99
[^7]: http://mysql.taobao.org/monthly/2017/11/09/

[^8]: https://www.cnblogs.com/huchong/p/10231719.html#_lab2_3_0
[^ 9]:[MySQL到底有多少种日志类型需要我们记住的！- lemon强烈推荐](https://cloud.tencent.com/developer/article/1181844)
[^10]:[玩转MySQL之八MySQL日志分类及简介](https://zhuanlan.zhihu.com/p/58011817)
[^ 11]: [MySQL自增主键用完了怎么办](https://www.cnblogs.com/rjzheng/p/10669043.html)