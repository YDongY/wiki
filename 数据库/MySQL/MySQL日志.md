---
title: MySQL 日志
description: 
published: true
date: 2021-03-24T16:14:46.335Z
tags: 
editor: markdown
dateCreated: 2021-03-24T16:14:28.527Z
---

# 1. 错误日志（error log）

对`MySQL`的启动、运行、关闭过程进行记录。该文件不仅记录了所有的错误信息，也记录了一些警告信息或者正确的信息。在默认情况下错误文件的文件名为**服务器的主机名**，所以错误文件名为`hostname.err`

默认保存路径在参数 DATADIR（数据目录）指定的目录中

```sql
mysql> select @@datadir;
+------------------------+
| @@datadir              |
+------------------------+
| /usr/local/mysql/data/ |
+------------------------+
1 row in set (0.00 sec)
```

查看日志文件名：

```sql
mysql> show variables like 'log_error';
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| log_error     | ./ydongy-PC.err |
+---------------+-----------------+
1 row in set (0.05 sec)
```

# 2. 二进制日志（binlog）

记录对`MySQL`数据库的执行更改的所有操作（`DDL`、`DCL`、`DML`），不包括`select`和`show`，这类操作本身并没有修改。记录语句以事件的形式保存，描述了数据的更改过程，属于逻辑格式的日志

- 用于复制，在主从复制中，从库利用主库上的 binlog 进行重播，实现主从同步。
- 用于数据库的基于时间点（point-in-time）的还原。

## 2.1 日志存储格式

二进制日志的格式分为 3 种：

- STATEMENT：日志记录的都是语句，每一条对数据造成修改的 SQL 语句都会记录在日志，通过 mysqlbinlog 工具可以查看每条语句的文本。主从复制的时候，从库 slave 会将日志解析为原文本，并在从库执行一次。优点是可读性强、日志量少、对 I/O 影响小，但是在某些情况 slave 的日志会出错
- ROW：保存的是每一行变更记录。例如执行一条 SQL ：`update test set name='123'`将全表的 name 更改为 123，对于 ROW 格式而言，会记录全表的每一行记录的变化情况。
- MIXED：**默认格式**，即混合了前两种格式，默认是以 STATEMENT 优先。

## 2.2 读取日志

```shell
mysqlbinlog binlog.000001
--or
mysqlbinlog binlog.000001 -v/-vv
```

## 2.3 删除日志

```sql
mysql> reset master
--or
mysql> purge master logs to 'xxx-bin.'
```

## 2.4 日志配置

```sql
-- 1. 查看二进制日志中当前使用的日志文件

mysql> show master status;
+-----------------+----------+--------------+------------------+-------------------+
| File            | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+-----------------+----------+--------------+------------------+-------------------+
| mysqlbin.000015 |     5703 |              | mysql            |                   |
+-----------------+----------+--------------+------------------+-------------------+
1 row in set (0.02 sec)

-- 2. 查看二进制日志内容

mysql> show binlog events;
+-----------------+-----+----------------+-----------+-------------+-----------------------------------------+
| Log_name        | Pos | Event_type     | Server_id | End_log_pos | Info                                    |
+-----------------+-----+----------------+-----------+-------------+-----------------------------------------+
| mysqlbin.000001 |   4 | Format_desc    |         1 |         123 | Server ver: 5.7.21-1-log, Binlog ver: 4 |
| mysqlbin.000001 | 123 | Previous_gtids |         1 |         154 |                                         |
| mysqlbin.000001 | 154 | Stop           |         1 |         177 |                                         |
+-----------------+-----+----------------+-----------+-------------+-----------------------------------------+
3 rows in set (0.00 sec)

-- 3. 查看二进制日志存放路径

mysql> show variables like 'datadir';
+---------------+------------------------+
| Variable_name | Value                  |
+---------------+------------------------+
| datadir       | /usr/local/mysql/data/ |
+---------------+------------------------+
1 row in set (0.00 sec)
```

## 2.5 配置文件参数

- `max_binlog_size`：单个二进制日志文件的最大值，超过该值，产生新的二进制日志，后缀名+1，并记录到`.index`文件，`MySQL5.0`之后默认为`1G`。
- `binlog-do-db`：表示写入那些库的日志，默为空，表示需要同步所有库的日志到二进制日志
- `binlog-ignore-db`：表示忽略写入那些库的日志，默为空，表示需要同步所有库的日志到二进制日志
- `binlog_format`：记录二进制日志的格式
  - `statement`：日志的逻辑`SQL`语句
  - `row`：记录表的每一行更改情况，主从复制更加安全
  - `mixed`：有系统决定，默认采用`statement`格式，但是根据不同的情况会不一样
- `binlog_cache_size`：记录未提交事务的二进制日志的缓存。当事务记录大于缓存，MySQL 将会把缓冲中的日志写入到一个临时文件中。通过`show global status`命令查看
  - `binlog_cache_use`：记录使用缓冲二进制日志的次数
  - `binglog_cache_disk_use`：记录了使用临时文件写入二进制日志的次数

## 2.6 二进制写入机制

事务执行过程中，先把日志写到 binlog cache，事务提交的时候，再把 binlog cache 写到 binlog 文件中。

从 binlog cache 到 binlog 文件分为两步：

1. 调用 write 将 binlog cache 写入到文件系统的 page cache
2. 调用 sync 将文件系统的 page cache 持久化到磁盘

write 和 fsync 的时机，是由参数 sync_binlog 控制的：

1. sync_binlog=0 ：表示每次提交事务都只 write，不 fsync；
2. sync_binlog=1 ：表示每次提交事务都会执行 fsync；
3. sync_binlog=N(N>1)：表示每次提交事务都 write，但累积 N 个事务后才 fsync。

# 3. 慢查询日志（slow query log）

通过设置一个阀值，将运行时间超过该阀值的所有`SQL`语句都记录到慢查询日志文件中，该阀值可以用过参数`long_query_time`来设定，默认值为10s。查看命令如下：

```sql
mysql> show variables like 'long_query_time';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+
1 row in set (0.00 sec)
```

在默认情况下。`MySQL`数据库并不启动慢查询日志，查看是否启动：

- 0：关闭
- 1：启动

```sql
mysql> select @@slow_query_log;
+------------------+
| @@slow_query_log |
+------------------+
|                0 |
+------------------+
1 row in set (0.00 sec)
```

文件存放位置：

```sql
mysql> select @@slow_query_log_file;
+------------------------------------------+
| @@slow_query_log_file                    |
+------------------------------------------+
| /usr/local/mysql/data/ydongy-PC-slow.log |
+------------------------------------------+
1 row in set (0.00 sec)
```

手动启动慢查询日志：

```shell
slow_query_log=1
slow_query_log_file=xxx/xxx/xxx-slow.log
long_query_time=2 # 阀值大小，执行时间必须大于该值，小于等于都不会记录
```

慢查询相关的参数`log_queries_not_using_indexes`，表示是否用到索引。开启之后，如果查询没有用到索引同样会记录到慢查询日志，查看是否打开：

```sql
mysql> show variables like 'log_queries_not_using_indexes';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| log_queries_not_using_indexes | OFF   |
+-------------------------------+-------+
1 row in set (0.00 sec)
```

配置文件启用：

```shell
log_queries_not_using_indexes=1
```

`MySQL 5.6.5`版本开始新增一个参数`log_throttle_queries_not_using_indexes`，表示每分钟允许记录到慢查询日志且未使用索引的`SQL`语句次数。默认值为0，表示不受限制。

```sql
mysql> show variables like 'log_throttle_queries_not_using_indexes';
+----------------------------------------+-------+
| Variable_name                          | Value |
+----------------------------------------+-------+
| log_throttle_queries_not_using_indexes | 0     |
+----------------------------------------+-------+
1 row in set (0.00 sec)
```

# 4. Redo Log

## 4.1 作用

重做日志是 InnoDB 存储引擎独有的日志文件，用来做数据库 crash recovery 的。保证事务的持久性。防止在发生故障的时间点，尚有脏页未写入磁盘，在重启 mysql 服务的时候，根据 redo log 进行重做，从而达到事务的持久性这一特性。

## 4.2 内存

重做日志只记录有关 innoDB 存储引擎的事务物理格式的日志。记录的关于每个页的物理数据修改信息，其 redo log 是顺序写入 redo log file 的物理文件中去的。在启动数据库时，文件不存在，则InnoDB 会根据配置参数，重新创建日志文件

## 4.3 物理存储

在默认情况下，每个 InnoDB 存储引擎的至少有一个重做日志组，每个重做日志组至少有两个重做日志文件，分别为 ib_logfile0 和 ib_logfile1。每个重做日志组中的重做日志文件大小相同 512 字节，并采用**循环覆盖**的方式写入。

相关配置：

- `innodb_log_file_size`：指定每个重做日志文件的大小
- `innodb_log_files_in_group`：指定日志文件组中重做日志文件的数量，默认为 2
- `innodb_mirrored_log_groups`：指定日志镜像文件组的数量，默认为 1
- `innodb_log_group_home_dir`：指定日志文件组所在的路径，默认为`./`，表示在数据库目录下。

## 4.4 日志刷盘规则

在 MySQL 里采用 **WAL（Write-Ahead Logging）\**技术，它的关键点就是\**先写日志，再写磁盘**。

redo log 的写入分为两部分，一部分是 redo log buffer ，另一部分是 redo log file。具体来说，当一个事务开始之后就会产生 redo log ，并且在事务的执行过程中就会先把 redo log 写入到 redo log buffer 之后在根据一定的规则把 redo log buffer 中的日志刷新到磁盘（默认大小 512 字节，此次操作 InnoDB 存储引擎会调用 fsync 确保重做日志写入磁盘）。

默认情况下事务每次提交的时候都会刷事务日志到磁盘中，这是通过配置 `innodb_flush_log_at_trx_commit` 来控制重做日志刷新磁盘的策略。有效值为 0、1、2，默认值是 1。

- 设置为 0 ， 表示每次事务提交时都只是把 redo log 留在 redo log buffer 中， 由 MySQL 的后台线程每隔 1s 把 redo log buffer 中的日志，调用 write 写到文件系统的 page cache，然后调用 fsync 持久化到磁盘。
- 设置为 1 ， 表示每次事务提交时都将 redo log 直接持久化到磁盘。
- 设置为 2， 表示每次事务提交时都只是把 redo log 写到 page cache 。 由 MySQL 的后台线程每隔 1s 将 page cache 的⽇志⽂件刷新到磁盘中。

![redolog.png](/assets/数据库/mysql/redolog.png)

> 注意，事务执行中间过程的 redo log 也是直接写在 redo log buffer 中的，这些 redo log 也会被后台线程一起持久化到磁盘。也就是说，一个没有提交的事务的 redo log，也是可能已经持久化到磁盘的。
{.is-warning}

实际上，除了 commit 操作外，还有其他场景会让一个没有提交的事务的 redo log 写入到磁盘中。

1. Master Thread 每秒一次执行刷新 Innodb_log_buffer 到重做日志文件，这个操作是固定的。
2. 当重做日志缓存可用空间少于一半时，重做日志缓存被刷新到重做日志文件。
3. 并行的事务提交的时候，顺带将其他事务的 redo log buffer 持久化到磁盘


