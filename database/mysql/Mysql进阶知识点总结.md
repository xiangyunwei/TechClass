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



## MySQL 进阶知识

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



### innoDB存储引擎的主键用自增方式还是UUID？

**结论**：为了提高性能 在InnoDB上尽量采用自增字段做主键 。

由于 innoDB 中的主键是聚簇索引，当数据插入时使用B+ 树结构，各条数据按主键顺序排放，使用自增方式生成的主键 ID 是有序的，方便数据插入，减少了插入的磁盘IO消耗。

`UUID`被设计为在空间和时间全球独一无二的数字，UUID 值是一个随机字符串，类似` 887212cf-72fc-11e7-bdf0-f0def1e6678c `这种。由于 UUID 是随机生成唯一值，所以插入数据库时的位置具有不确定性，无序插入会存在许多内存碎片，内存空间的占用量也会比自增主键大，区间查找也没自增主键性能优。



### 无符号整型自增主键达到最大值再插入会怎么样？

无符号整型主键的范围是 `0～4294967295` ，约`43亿` 

达到最大值再插入会报错：`  Duplicate entry '4294967295' for key 'PRIMARY' `



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
[^9]:[MySQL到底有多少种日志类型需要我们记住的！- lemon强烈推荐](https://cloud.tencent.com/developer/article/1181844)
[^10]:[玩转MySQL之八MySQL日志分类及简介](https://zhuanlan.zhihu.com/p/58011817)
[^11]: [MySQL自增主键用完了怎么办](https://www.cnblogs.com/rjzheng/p/10669043.html)
[^12]: [MySQL中char与varchar区别，varchar最大长度是多少？](https://www.cnblogs.com/jewave/p/6214540.html)
[^13]:  [MySQL中 int(11)和int(10)有没有区别](https://blog.csdn.net/ZBylant/article/details/86572567?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight)