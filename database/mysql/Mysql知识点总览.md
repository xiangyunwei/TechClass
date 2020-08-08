### Mysql数据类型

CHAR VARCHAR 

### 事务ACID

[参考晚期文章](https://mp.weixin.qq.com/s?__biz=MzIwMjM4NDE1Nw==&mid=2247483807&idx=1&sn=760d3b36c3742b0413b7f6f095506fd5&chksm=96de37eda1a9befb371bf03653df7528921262c95ab1483b4ecb86a25a8315a9598c723adaa5&token=969827033&lang=zh_CN#rd)

举个栗子，转账的操作可以简化抽成一个事务，包含如下步骤：

1. 查询CMBC账户的余额是否大于100万
2. 从CMBC账户余额中减去100万
3. 在ICBC账户余额中增加100万

以下语句对应创建了一个转账事务：

```
1START TRANSACTION;2SELECT balance FROM CMBC WHERE username='lemon';3UPDATE CMBC SET balance = balance - 1000000.00 WHERE username = 'lemon';4UPDATE ICBC SET balance = balance + 1000000.00 WHERE username = 'lemon';5COMMIT;
```

ACID其实是事务特性的英文首字母缩写，具体的含义是这样的：

- 原子性（atomicity)
  一个事务必须被视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作。
- 一致性（consistency)
  数据库总是从一个一致性的状态转换到另外一个一致性的状态。在前面的例子中，一致性确保了，即使在执行第三、四条语句之间时系统崩溃，CMBC账户中也不会损失100万，不然 lemon要哭死，因为事务最终没有提交，所以事务中所做的修改也不会保存到数据库中。
- 隔离性（isolation)
  通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的。在前面的例子中，当执行完第三条语句、第四条语句还未开始时，此时如果有其他人准备给 lemon 的 CMBC 账户存钱，那他看到的CMBC账户里还是有100万的。
- 持久性（durability)
  一旦事务提交，则其所做的修改就会永久保存到数据库中。此时即使系统崩溃，修改的数据也不会丢失。持久性是个有点模糊的概念，因为实际上持久性也分很多不同的级别。有些持久性策略能够提供非常强的安全保障，而有些则未必。而且「不可能有能做到100%的持久性保证的策略」否则还需要备份做什么。

![](F:\github\TechClass\database\mysql\mysql事务详解\image\ACID.png)



### 事务是如何通过日志来实现

事务日志是通过redo和innodb的存储引擎日志缓冲（Innodb log buffer）来实现的，

事务是通过事务日志（transaction log）来实现原子性和持久性的，而事务日志又是通过回滚日志（undo log）和重做日志（redo log）来实现，前者用于对事务的影响进行撤销，后者在错误处理时对已经提交的事务进行重做，它们能保证两点：

1. 发生错误或者需要回滚的事务能够成功回滚（原子性）；
2. 在事务提交后，数据没来得及写会磁盘就宕机时，在下次重新启动后能够成功恢复数据（持久性）；

在数据库中，这两种日志经常都是一起工作的，我们可以将它们整体看做一条事务日志，其中包含了事务的 ID、修改的行元素以及修改前后的值。

![Transaction-Log](https://img.draveness.me/2017-08-20-Transaction-Log.jpg-1000width)

一条事务日志同时包含了修改前后的值，能够非常简单的进行回滚和重做两种操作

事务开始时，会记录该事务的事务序号 LSN (log sequence number) 。

事务执行时，会往 InnoDB 存储引擎的日志缓存里面插入事务日志；

事务提交时，必须将存储引擎的日志缓冲写入磁盘，也就是写数据前，需要先写日志。



### 存储引擎的区别

#### MySQL中 MyISAM 存储引擎与  InnoDB  引擎 5 大区别

- InnoDB支持事物，而MyISAM不支持事物
- .InnoDB支持行级锁，而MyISAM支持表级锁
- InnoDB支持MVCC, 而MyISAM不支持
- InnoDB支持外键，而MyISAM不支持
- InnoDB不支持全文索引，而MyISAM支持。

给你一张表，简单罗列两种引擎的主要区别，如下图。

![引擎对比](https://mmbiz.qpic.cn/mmbiz_png/ceNmtYOhbMQs4Ar4C7lr5CFmTkzO5qFP4ziaEN8O2vgic8ibP9RnGibTDcVaAxTKfTxeicpCrYquzWXRstmdviaCrSzA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



##### InnoDB  引擎的4大特性

插入缓冲（insert buffer),

二次写(double write)

自适应哈希索引(ahi)

预读(read ahead)



##### 同样的 selectcount(*) 哪个更快，为什么

MyISAM 更快，因为 MyISAM 内部维护了一个计数器，可以直接调取。



### Mysql索引

**查看索引**：`SHOW INDEX FROM <tablename>; `

### MySql中有哪些锁

- MyISAM支持表锁，InnoDB支持表锁和行锁，默认为行锁
- 表级锁：开销小，加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量最低
- 行级锁：开销大，加锁慢，会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高

### 性能优化方法

SHOW GLOBAL STATUS   
SHOW PROCESSLIST



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

#### 存储过程和函数的区别?

**相同点**

- 存储过程和函数都是为了可重复的执行操作数据库的 sql 语句的集合。

- 存储过程和函数都是一次编译，就会被缓存起来，下次使用就直接命中已经编译好的 sql 语句，不需要重复使用。减少网络交互，减少网络访问流量。

**不同点**

- 标识符不同，函数的标识符是 function，存储过程是 proceduce。

- 函数中有返回值，且必须有返回值，而过程没有返回值，但是可以通过设置参数类型（in,out)来实现多个参数或者返回值。

- 存储函数使用 select 调用，存储过程需要使用 call 调用。select 语句可以在存储过程中调用，但是除了 select..into 之外的 select 语句都不能在函数中使用。

- 通过 in out 参数，过程相关函数更加灵活，可以返回多个结果。

### 什么是触发器

在对表数据进行变动的时候进行具体的操作，有六种，分别为增删改的前后操作。

```sql
create trigger trigger_name 
AFTER|BEFORE select|update|delete
on 表
for each row
trigger_stmt
```

- 只有表才支持触发器，视图和临时表都不支持
- 触发器不支持更新和覆盖，修改必须先删除然后创建

### 什么是分区表

分区表是将大表的数据分成称为分区的许多小的子集，常见分区类型：Range、List、Hash、Key

```sql
查看是否支持分区表 show plugins 
如果有partition就说明支持
在创建表时字符集后增加 partition by 分区类型(字段) partitions 4;
添加分区 alter table 表 add partition (partition p4 values less than(2018))
```

### MySql 有哪些类型日志

**主要有四种日志文件**

错误日志：记录启动，运行或者停止 mysql 时出现的问题；
查询日志：记录所有msyql的活动
二进制日志：记录更新过数据的所有语句
慢查询日志：记录查询缓慢的任何查询
**中继日志**：中继日志也是二进制日志，用来给slave 库恢复
**事务日志**：重做日志 redo 和回滚日志 undo



### MySQL binlog的几种日志录入格式以及区别

**Statement**：每一条会修改数据的sql都会记录在binlog中。

**优点**：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。(相比row能节约多少性能 与日志量，这个取决于应用的SQL情况，正常同一条记录修改或者插入row格式所产生的日志量还小于Statement产生的日志量，但是考虑到如果带条 件的update操作，以及整表删除，alter表等操作，ROW格式会产生大量日志，因此在考虑是否使用ROW格式日志时应该跟据应用的实际情况，其所 产生的日志量会增加多少，以及带来的IO性能问题。)

**缺点**：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的 一些相关信息，以保证所有语句能在slave得到和在master端执行时候相同 的结果。另外mysql 的复制,像一些特定函数功能，slave可与master上要保持一致会有很多相关问题(如sleep()函数， last_insert_id()，以及user-defined functions(udf)会出现问题).

**使用以下函数的语句也无法被复制**：

- LOAD_FILE()
- UUID()
- USER()
- FOUND_ROWS()
- SYSDATE() (除非启动时启用了 --sysdate-is-now 选项)

同时在INSERT …SELECT 会产生比 RBR 更多的行级锁

**Row**:不记录sql语句上下文相关信息，仅保存哪条记录被修改。

**优点**： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以rowlevel的日志内容会非常清楚的记录下 每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题

**缺点**:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容,比 如一条update语句，修改多条记录，则binlog中每一条修改都会有记录，这样造成binlog日志量会很大，特别是当执行alter table之类的语句的时候，由于表结构修改，每条记录都发生改变，那么该表每一条记录都会记录到日志中。

**Mixedlevel**: 是以上两种level的混合使用，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则 采用row格式保存binlog,MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择 一种.新版本的MySQL中队row level模式也被做了优化，并不是所有的修改都会以row level来记录，像遇到表结构变更的时候就会以statement模式来记录。至于update或者delete等修改数据的语句，还是会记录所有行的变更。



### 什么 是MySql 权限表

Mysql服务器通过权限表来控制用户对数据库的访问，权限表存放在mysql数据库里，由mysql_install_db 脚本初始化。分别是 user，db，table_priv，columns_priv 和 host

### MySql服务默认端口号是多少

3306

### 回答一个主键 ID 问题

插入4 条记录，主键自增ID，分别是 0 1 2 3， 删除ID 2 3 的记录，重启MySql， 再插入一条记录，此时插入记录的 ID 是多少？

> 如果表的类型是 MyISAM, 那么是 4
> 因为 MylSAM表会把自增主键的最大ID记录到数据文件里,重启MSQL自增主键的最大D也不会丢失
> 如果表的类型是 InnoDB, 那么是2
> nnoDB表只是把自增主键的最大ID记录到内存中,所以重启数据库或者是对表进行 OPTIMIZE操作, 都导致最大 ID 丢失

###  CHAR和VARCHAR的区别

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



### MySQL的复制原理以及流程

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

