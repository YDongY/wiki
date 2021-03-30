---
title: Explain 执行计划
description: 
published: true
date: 2021-03-24T16:28:16.580Z
tags: 
editor: markdown
dateCreated: 2021-03-24T16:28:16.580Z
---

<!--# 1. 优化概述

数据库性能取决于**数据库级别**的几个因素，例如表，查询和配置设置。这些软件构造导致在硬件级别执行 CPU 和 I/O 操作，必须将这些操作最小化并使其尽可能高效。在研究数据库性能时，主要掌握数据库层面的的优化规则，并使用时钟时间来衡量性能。

在数据库层面主要通过一下几个方面作为参考：

- 表的结构是否正确？
- 是否使用到索引，或者导致索引失效？
- 否为每个表使用了适当的存储引擎，并利用了所使用的使用每个存储引擎的优势和功能？
- 服务器的参数设置是否恰当？例如：缓冲区的大小
- 数据库是否正确使用锁的机制？

以上几个方面的归根结底还是数据库的`SQL`操作，所以我们需要对`SQL`语句进行分析以及优化，达到整个数据库层面的优化。

# 2. 优化器 Optimizer

在`MySQL`的整体架构中有一个优化器组件（`Optimizer`），作用就是当我们执行一条`SQL`语句的时候，`MySQL`的优化器可能会觉得我们的`SQL`不是它认为最优的方案，会把我们`SQL`语句的加载顺序给更换一下，之后才会执行。所以为了避免大量的自动化优化操作，以及想要定义我们自己认为最有方案的`SQL`（例如使用索引），此时我们就需要知道我们的`SQL`语句真正被执行的时候是什么样的。

同样`MySQL`也为我们提供了一个`Explain`的关键字，使用`EXPLAIN`关键字就可以**模拟优化器执行`SQL`语句**，从而知道`MySQL`是如何处理我们的`SQL`语句的。通过分析结果来优化查询语句或是结构的性能瓶颈，下面就来看一下他的执行计划。 -->

## 1. Explain 分析执行计划

下面是一个简单的例子：

```sql
explain select * from user;
```

- `Explain`输出的列的相关介绍

| 列名            | 介绍                                                         |
| --------------- | ------------------------------------------------------------ |
| `id`            | select查询的序列号                                           |
| `select_type`   | 查询的类型                                                   |
| `table`         | 显示这一行的数据是关于哪张表的                               |
| `partitions`    | 匹配的分区                                                   |
| `type`          | 表示表的连接类型                                             |
| `possible_keys` | 显示可能应用在这张表中的索引                                 |
| `key`           | 实际使用的索引。如果为null则没有使用索引                     |
| `key_len`       | 表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。在不损失精确性的情况下，长度越短越好 |
| `ref`           | 显示索引那一列被使用了，如果可能的话，是一个常数。那些列或常量被用于查找索引列上的值 |
| `rows`          | 根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数 |
| `filtered`      | 按表条件过滤的行百分比                                       |
| `Extra`         | 执行情况的说明和描述                                         |

## 1.1 环境准备

1.新建数据库

```sql
create database db01 charset=utf8;
```

2.切换数据库

```sql
use db01;
```

3.创建表

![model.png](/assets/数据库/mysql/explain/model.png)

```sql
CREATE TABLE `t_role` (
  `id` varchar(32) NOT NULL,
  `role_name` varchar(255) DEFAULT NULL,
  `description` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unique_role_name` (`role_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;


CREATE TABLE `t_user` (
  `id` varchar(32) NOT NULL,
  `username` varchar(45) NOT NULL,
  `age` int NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unique_user_username` (`username`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;


CREATE TABLE `user_role` (
  `id` int(11) NOT NULL auto_increment ,
  `user_id` varchar(32) DEFAULT NULL,
  `role_id` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `fk_ur_user_id` (`user_id`),
  KEY `fk_ur_role_id` (`role_id`),
  CONSTRAINT `fk_ur_role_id` FOREIGN KEY (`role_id`) REFERENCES `t_role` (`id`) ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `fk_ur_user_id` FOREIGN KEY (`user_id`) REFERENCES `t_user` (`id`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
4.添加数据

```sql
insert into `t_user` (`id`, `username`, `age`) values('1','超级管理员',50);
insert into `t_user` (`id`, `username`, `age`) values('2','系统管理员',30);
insert into `t_user` (`id`, `username`, `age`) values('3','老师',26);
insert into `t_user` (`id`, `username`, `age`) values('4','学生1',18);
insert into `t_user` (`id`, `username`, `age`) values('5','学生2',19);
insert into `t_user` (`id`, `username`, `age`) values('6','学生3',20);
insert into `t_user` (`id`, `username`, `age`) values('7','学生4',18);


INSERT INTO `t_role` (`id`, `role_name`, `description`) VALUES('1','学生','学生');
INSERT INTO `t_role` (`id`, `role_name`, `description`) VALUES('2','老师','老师');
INSERT INTO `t_role` (`id`, `role_name`, `description`) VALUES('3','教学管理员','教学管理员');
INSERT INTO `t_role` (`id`, `role_name`, `description`) VALUES('4','管理员','管理员');
INSERT INTO `t_role` (`id`, `role_name`, `description`) VALUES('5','超级管理员','超级管理员');

INSERT INTO user_role(id,user_id,role_id) VALUES(NULL, '1', '5'),(NULL, '2', '4'),(NULL, '3', '2'),(NULL, '4', '1'),(NULL, '5', '1'),(NULL, '6', '1'),(NULL, '7', '1') ;
```


## 1.2 explain 之 id

id 字段是 select查询的序列号，是一组数字，表示的是查询中执行select子句或者是操作表的顺序。id 情况有三种 ：

**1. id 相同表示加载表的顺序是从上到下**

```sql
explain select u.* from t_user u,t_role r,user_role ur  where u.id=ur.user_id and r.id=ur.role_id;
```

![id-1.png](/assets/数据库/mysql/explain/id-1.png)

注：加载顺序`t_user>user_role>t_role`

**2. id不同，如果是**子查询**，id的序号会递增，id值越大优先级越高，越先被执行**

```sql
EXPLAIN SELECT * FROM t_role WHERE id = (SELECT role_id FROM user_role WHERE user_id = (SELECT id FROM t_user WHERE username = '学生1'));
```

![id-2.png](/assets/数据库/mysql/explain/id-2.png)

注：加载顺序`t_user>user_role>t_role`

**3. id 有相同，也有不同，同时存在。id相同的可以认为是一组，从上往下顺序执行；在所有的组中，id的值越大，优先级越高，越先执行。**

```sql
EXPLAIN SELECT * FROM t_role r , (SELECT * FROM user_role ur WHERE ur.`user_id` = '2') a WHERE r.id = a.role_id ;
```

![id-3.png](/assets/数据库/mysql/explain/id-3.png)

## 1.3 explain 之 select_type

|      名称      |                             解释                             |
| :------------: | :----------------------------------------------------------: |
|    `SIMPLE`    |      简单的 select 查询，查询中**不包含子查询或者UNION**![select_type-1.png](/assets/数据库/mysql/explain/select_type-1.png)       |
|   `PRIMARY`    |       查询中若包含任何复杂的子部分，最外层查询则被标记![select_type-2.png](/assets/数据库/mysql/explain/select_type-2.png)       |
|   `SUBQUERY`   |             在 SELECT 或者 WHERE 列表中包含了子查询              |
|   `DERIVED`    | 在 FROM 列表中包含的子查询被标记为 DERIVED（衍生），MySQL 会递归执行这些子查询，把结果放在临时表里。 |
|    `UNION`     | 若第二个 SELECT 出现在 UNION 之后，则被标记为 UNION，若 UNION 包含在 FROM 子句的子查询中，外层 SELECT 将被标记为：DERIVED![select_type-3.png](/assets/数据库/mysql/explain/select_type-3.png) |
| `UNION RESULT` |                  从UNION表获取结果的SELECT                   |

## 1.4 explain 之 table

展示这一行的数据是关于哪一张表的，例如：三表联合查询
```sql
select * from t_user u,t_role r,user_role ur  where u.id=ur.user_id and r.id=ur.role_id;
```
![table](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630142755429-1498859432.png)


## 1.5 explain 之 type

type 显示的是访问类型，是较为重要的一个指标，可取值为：

| type     | 含义                                                         |
| :------- | :----------------------------------------------------------- |
| `NULL`   | `MySQL`不访问任何表，索引，直接返回结果<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154340993-965665741.png)|
| `system` | 表只有一行记录(等于系统表)，这是`const`类型的特例，一般不会出现 |
| `const`  | 表示通过索引一次就找到了，`const` 用于比较primary key 或者 unique 索引。因为只匹配一行数据，<br>所以很快。如将主键置于where列表中，`MySQL` 就能将该查询转换为一个常亮。<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154401325-914129096.png) |
| `eq_ref` | 类似ref，区别在于使用的是唯一索引，使用主键的关联查询，关联查询出的记录只有一条。常见于主键或唯一索引扫描<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154415328-613536266.png) |
| `ref`    | **非唯一性索引扫描，返回匹配某个单独值的所有行**。本质上也是一种索引访问，返回所有匹配某个单独值的所有行（多个）<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154434289-505253328.png) |
| `range`  |只检索给定返回的行，使用一个索引来选择行。 where 之后出现 between ， < , > , in 等操作。<br> ![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154452862-198984978.png) |
| `index`  | index 与 ALL的区别为  index 类型只是遍历了索引树， 通常比ALL 快， ALL 是遍历数据文件。<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154518997-1773009198.png) |
| `all`    |将遍历全表以找到匹配的行，不使用索引，或者索引失效<br> ![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630154534768-1961264570.png) |


>结果值从最好到最差顺序：
NULL > system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL


## 1.6 explain 之 possible_keys 和 key

- `possible_keys`：表示可能用到的索引
- `key`：实际用到的索引

![possible_keys&key](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630161347618-373851157.png)

另外一种情况就是`MySQL`认为我们会使用索引，但是可能由于一些原因导致实际没有用到索引。

![key](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630161404639-2071891404.png)


## 1.7 explain 之 rows

查询时扫描行的数量。

![rows](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630161404639-2071891404.png)

图上我们发现，当我们使用索引的时候，只扫描了一行就得到结果，索引失效而扫描了所有的行。

## 1.8 explain 之 extra

其他的额外的执行计划信息，在该列展示 。

| extra               | 含义                                                         |
| :------------------ | ------------------------------------------------------------ |
| `using  filesort`   | 说明`mysql`会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取， 称为 “文件排序”, 效率低。简称：由于某种原因，`mysql`没法使用我们定义的索引而是，`mysql`自己在内部进行了排序<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165329917-418463659.png) |
| `using  temporary`  | 使用了临时表保存中间结果，`MySQL`在对查询结果排序时使用临时表。常见于 order by 和 group by； 效率低<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165344596-1897564703.png) |
| `using  index`      | 表示相应的select操作中使用了覆盖索引（`Coveing Index`）,避免访问了表的数据行，效率不错！<br/>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165355753-1492090949.png)<br>如果同时出现using where，表明索引被用来执行索引键值的查找；<br/>如果没有同时出现using where，表面索引用来读取数据而非执行查找动作。<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165403139-2095121819.png) |
| `Using where`       | 表面使用了where过滤<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165411348-1711885607.png)  |
| `using join buffer` | 使用了连接缓存<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165417951-1835705501.png)       |
| `impossible where`  | where子句的值总是false，不能用来获取任何元素<br>![](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630165730777-304862818.png) |

# 2. 避免索引失效

![表](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630182913217-1498944272.png)

## 2.1 全值匹配

> 我们查询的字段以及顺序最好按照索引的顺序进行编写`SQL`

![全值匹配](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630182938051-1756752035.png)

## 2.2 最佳左前缀法则

> 如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且不跳过索引中的列。

![最佳左前缀法则](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183006394-958439524.png)

## 2.3 索引计算

> 不在索引列上做任何操作（计算、函数、(自动or手动)类型转换），会导致索引失效而转向全表扫描

![索引计算失效](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183027529-953111201.png)

## 2.4 范围查询

> `MySQL`不能使用索引中范围条件右边的列

![范围查询](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183047068-2075604820.png)

## 2.5 覆盖索引

> 尽量使用覆盖索引（只访问索引的查询，索引列和查询列一致），减少select *

![覆盖索引](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183116109-1554290709.png)

## 2.6 不等于

> `MySQL`在使用不等于(!= 或者<>)的时候无法使用索引会导致全表扫描

![不等于](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183137396-190849180.png)

## 2.7 Null

> is not null 也无法使用索引,但是is null是可以使用索引的

![Null](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183158551-1882973071.png)

## 2.8 通配符

> like以通配符开头`MySQL`索引失效会变成全表扫描的操作

![通配符](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183216629-377353429.png)

## 2.9 or

> or 来连接时会索引失效

![or索引失效](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183235033-1945648408.png)

## 2.10 varchar

> `varchar`类型不加引号，`MySQL`默认类型转换导致索引失效

![varchar](https://img2020.cnblogs.com/blog/1482060/202006/1482060-20200630183253784-1068805961.png)


# 3. 慢查询日志

## 3.1 配置慢查询

默认情况下，MySQL数据库没有开启慢查询日志，需要我们手动来设置这个参数。当然，如果不是调优需要的话，一般不建议启动该参数，因为开启慢查询日志会或多或少带来一定的性能影响。慢查询日志支持将日志记录写入文件

默认情况下`slow_query_log`的值为`OFF`，表示慢查询日志是禁用的，可以通过设置`slow_query_log`的值来开启,

```sql
mysql> SHOW VARIABLES LIKE '%slow_query_log%';
+---------------------+-----------------------------------+
| Variable_name       | Value                             |
+---------------------+-----------------------------------+
| slow_query_log      | OFF                               |
| slow_query_log_file | /var/lib/mysql/ydongy-PC-slow.log |
+---------------------+-----------------------------------+
2 rows in set (0.01 sec)
```

- **开启慢查询日志**
```sql
mysql> set global slow_query_log=1; # 开启
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW VARIABLES LIKE '%slow_query_log%';
+---------------------+-----------------------------------+
| Variable_name       | Value                             |
+---------------------+-----------------------------------+
| slow_query_log      | ON                                |
| slow_query_log_file | /var/lib/mysql/ydongy-PC-slow.log |
+---------------------+-----------------------------------+
2 rows in set (0.01 sec)
```

使用`set global slow_query_log=1`开启了慢查询日志只对**当前数据库生效**，如果MySQL重启后则会失效。如果要永久生效，就必须修改配置文件my.cnf（其它系统变量也是如此）如下两行配置进my.cnf文件：
```cnf
[mysqld]

slow_query_log =1
slow_query_log_file=/var/lib/mysql/ydongy-PC-slow.log # 日志存放位置
```

关于慢查询的参数slow_query_log_file ，它指定慢查询日志文件的存放路径，系统默认会给一个缺省的文件host_name-slow.log（如果没有指定参数slow_query_log_file的话）

- **本地日志**

```conf
root@ydongy-PC:/var/lib/mysql# cat ydongy-PC-slow.log
/usr/sbin/mysqld, Version: 5.7.21-1-log ((Debian)). started with:
Tcp port: 3306  Unix socket: /var/run/mysqld/mysqld.sock
Time                 Id Command    Argument

# Time: 2020-06-29T14:16:24.417302Z
# User@Host: root[root] @ localhost []  Id:    17
# Query_time: 4.000161  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 0
SET timestamp=1593440184;
select sleep(4);

# Time: 2020-06-29T15:04:36.134142Z
# User@Host: root[root] @ localhost []  Id:     7
# Query_time: 256.515803  Lock_time: 0.000074 Rows_sent: 0  Rows_examined: 0
# ---------Query_time:持续时间,Lock_time:锁时间－－－－－－－－－－
use bigData;　# 指定那个库
SET timestamp=1593443076;　# 时间戳
call insert_emp(1000001,500000);　# 执行的命令

# Time: 2020-06-29T16:31:49.429265Z
# User@Host: root[root] @ localhost []  Id:     7
# Query_time: 20.869845  Lock_time: 0.000000 Rows_sent: 0  Rows_examined: 0
use db01;
SET timestamp=1593448309;
stop slave;
/usr/sbin/mysqld, Version: 5.7.21-1-log ((Debian)). started with:
Tcp port: 3306  Unix socket: /var/run/mysqld/mysqld.sock
Time                 Id Command    Argument
```

## 3.2 慢查询阀值

那么开启了慢查询日志后，什么样的SQL才会记录到慢查询日志里面呢？

由参数`long_query_time`控制，默认情况下`long_query_time`的值为10秒，命令：
```sql
mysql> SHOW VARIABLES LIKE 'long_query_time%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)

mysql>
```

可以使用命令修改，也可以在my.cnf参数里面修改。假如运行时间正好等于long_query_time的情况，并不会被记录下来。也就是说，在mysql源码里是判断大于`long_query_time`，而非大于等于。

- **设置阀值**

 使用命令修改为阙值到1秒钟的就是慢sql 修改后发现long_query_time并没有改变，需要重新连接，或者新开一个会话

```sql
set global long_query_time=1;
```

## 3.3 日志分析工具 mysqldumpslow

```shell
$ mysqldumpslow --help
Usage: mysqldumpslow [ OPTS... ] [ LOGS... ]

Parse and summarize the MySQL slow query log. Options are

  --verbose    verbose
  --debug      debug
  --help       write this text to standard output

  -v           verbose
  -d           debug
  -s ORDER     what to sort by (al, at, ar, c, l, r, t), 'at' is default　#按照何种方式排序；
                al: average lock time
                ar: average rows sent
                at: average query time
                 c: count # 次数
                 l: lock time　# 锁定时间
                 r: rows sent　# 返回记录
                 t: query time  # 查询行数
  -r           reverse the sort order (largest last instead of first)
  -t NUM       just show the top n queries # 即为返回前面多少条的数据；
  -a           don't abstract all numbers to N and strings to 'S'
  -n NUM       abstract numbers with at least n digits within names
  -g PATTERN   grep: only consider stmts that include this string # 后边搭配一个正则匹配模式，大小写不敏感的；
  -h HOSTNAME  hostname of db server for *-slow.log filename (can be wildcard),
               default is '*', i.e. match all
  -i NAME      name of server instance (if using mysql.server startup script)
  -l           don't subtract lock time from total time
```
**例子：**
- 返回记录集最多的10个
```shell
mysqldumpslow -s r -t 10 /var/lib/mysql/ydongy-PC-slow.log
```
- 访问次数最多的10个
```shell
mysqldumpslow -s c -t 10 /var/lib/mysql/ydongy-PC-slow.log
```
- 按照时间排序的前10条里面含有左连接的查询语句
```shell
mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/ydongy-PC-slow.log | more
```

# 4. Show Profile

mysql提供可以用来分析当前会话中语句执行的资源消耗情况。可以用于SQL的调优的测量，默认情况下，参数处于关闭状态，并保存最近15次的运行结果

- 查看时候开启
```sql
mysql>   Show  variables like 'profiling';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| profiling     | OFF   |
+---------------+-------+
1 row in set (0.06 sec)
```

- **开启**

```sql
set profiling=1;
```

- **查看最近sql执行的信息**
```sql
mysql> show profiles;
+----------+------------+----------------------------------+
| Query_ID | Duration   | Query                            |
+----------+------------+----------------------------------+
|        1 | 0.01518875 | Show  variables like 'profiling' |
+----------+------------+----------------------------------+
1 row in set, 1 warning (0.02 sec)
```
- **诊断SQL**

```sql
show profile cpu,block io for query  n  (n为上一步前面的问题SQL数字号码);

+----------------------+----------+----------+------------+--------------+---------------+
| Status               | Duration | CPU_user | CPU_system | Block_ops_in | Block_ops_out |
+----------------------+----------+----------+------------+--------------+---------------+
| starting             | 0.009082 | 0.000089 |   0.000087 |          160 |             0 |
| checking permissions | 0.000012 | 0.000006 |   0.000006 |            0 |             0 |
| Opening tables       | 0.000012 | 0.000006 |   0.000005 |            0 |             0 |
| init                 | 0.000032 | 0.000016 |   0.000016 |            0 |             0 |
| System lock          | 0.000005 | 0.000003 |   0.000003 |            0 |             0 |
| optimizing           | 0.000003 | 0.000001 |   0.000001 |            0 |             0 |
| optimizing           | 0.000003 | 0.000001 |   0.000002 |            0 |             0 |
| statistics           | 0.000008 | 0.000004 |   0.000004 |            0 |             0 |
| preparing            | 0.000010 | 0.000005 |   0.000004 |            0 |             0 |
| statistics           | 0.000009 | 0.000005 |   0.000005 |            0 |             0 |
| preparing            | 0.000004 | 0.000002 |   0.000002 |            0 |             0 |
| executing            | 0.000005 | 0.000002 |   0.000002 |            0 |             0 |
| Sending data         | 0.000005 | 0.000002 |   0.000002 |            0 |             0 |
| executing            | 0.000002 | 0.000001 |   0.000001 |            0 |             0 |
| Sending data         | 0.000893 | 0.000450 |   0.000443 |            0 |             0 |
| end                  | 0.005028 | 0.000041 |   0.000041 |          248 |             0 |
| query end            | 0.000012 | 0.000006 |   0.000006 |            0 |             0 |
| closing tables       | 0.000003 | 0.000001 |   0.000001 |            0 |             0 |
| removing tmp table   | 0.000009 | 0.000005 |   0.000004 |            0 |             0 |
| closing tables       | 0.000006 | 0.000003 |   0.000003 |            0 |             0 |
| freeing items        | 0.000016 | 0.000008 |   0.000009 |            0 |             0 |
| cleaning up          | 0.000032 | 0.000025 |   0.000024 |            0 |             0 |
+----------------------+----------+----------+------------+--------------+---------------+
22 rows in set, 1 warning (0.00 sec)
```

| 参数               | 解释                                                         |
| ------------------ | ------------------------------------------------------------ |
| `all`              | 显示所有的开销信息                                           |
| `BLOCK IO`         | 显示块IO相关开销                                             |
| `CONTEXT SWITCHES` | 上下文切换相关开销                                           |
| `CPU`              | 显示CPU相关开销信息                                          |
| `IPC`              | 显示发送和接收相关开销信息                                   |
| `MEMORY`           | 显示内存相关开销信息                                         |
| `PAGE FAULTS`      | 显示页面错误相关开销信息                                     |
| `SOURCE`           | 显示和`Source_function`，`Source_file`，`Source_line`相关的开销信息 |
| `SWAPS`            | 显示交换次数相关开销的信息                                   |

- **结果信息**
  * `converting HEAP to MyISAM`：查询结果太大，内存都不够用了往磁盘上搬了。
  * `Creating tmp table`：创建临时表
  * `Copying to tmp table on disk`：把内存中临时表复制到磁盘。
  * `locked`：锁
