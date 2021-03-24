---
title: MyISAM 表锁
description: 
published: true
date: 2021-03-24T15:53:39.112Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:52:26.001Z
---

# 1. 查询表锁争用情况

```sql
mysql> show status like 'table%';
+----------------------------+-------+
| Variable_name              | Value |
+----------------------------+-------+
| Table_locks_immediate      | 13    |
| Table_locks_waited         | 0     |
| Table_open_cache_hits      | 5     |
| Table_open_cache_misses    | 9     |
| Table_open_cache_overflows | 0     |
+----------------------------+-------+
5 rows in set (0.00 sec)
```

- `Table_locks_immediate`：指的是能够立即获得表级锁的次数，每立即获取锁，值加1。

- `Table_locks_waited`：指的是不能立即获取表级锁而需要等待的次数，每等待一次，该值加1，此值高说明存在着较为严重的表级锁争用情况。

# 2. 表锁模式

- 读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会互相影响，但是会阻塞其他用户的写请求
- 写锁（排它锁）：当前操作没有完成之前，它会阻断其他写锁和读锁。

MyISAM 表的读操作和写操作之间、写操作和写操作之间是串行。

# 3. 如何加表锁

`MyISAM` 在执行查询语句`SELECT`前，会自动给涉及的所有表加**读锁**，在执行更新操作`UPDATE、DELETE、INSERT`前，会自动给涉及的表加**写锁**，这个过程并不需要用户干预，因此，用户一般不需要直接用 `LOCK TABLE` 命令给 MyISAM 表显式加锁。

显式加表锁语法：

```sql
加读锁 ： lock table table_name read;

加写锁 ： lock table table_name write；
```

显式解锁语法：

```sql
unlock tables;
```

虽然`MyIASM`默认加了表锁，但是我们仍然可以手动加锁，具体通过案例来了解一下加锁之后，我们操作数据库会有什么影响。

# 4. 测试数据准备

在进行测试之前我们需要准备测试环境和相关数据库以及表，并且把`MySQL`的存储引擎指定为`MyISAM`，如下先创建一个数据库并切换到数据库：

```sql
create database demo2 default charset=utf8;
```

接着创建表，本次我们测试的是`MyISAM`存储引擎，所以要创建表时要指定存储引擎（说明存储引擎是作用于表，而不是作用于数据库，也就是说一个数据库有多个表，而多个表可以使用不同的存储引擎）

```sql
CREATE TABLE `tb_book` (
  `id` INT(11) auto_increment,
  `name` VARCHAR(50) DEFAULT NULL,
  `publish_time` DATE DEFAULT NULL,
  `status` CHAR(1) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=myisam DEFAULT CHARSET=utf8 ;

-- 插入数据

INSERT INTO tb_book (id, name, publish_time, status) VALUES(NULL,'高性能MySQL','2088-08-01','1');
INSERT INTO tb_book (id, name, publish_time, status) VALUES(NULL,'MySQL技术内幕','2088-08-08','0');

CREATE TABLE `tb_user` (
  `id` INT(11) auto_increment,
  `name` VARCHAR(50) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=myisam DEFAULT CHARSET=utf8 ;

-- 插入数据

INSERT INTO tb_user (id, name) VALUES(NULL,'张三');
INSERT INTO tb_user (id, name) VALUES(NULL,'李四');
```

# 5. 读锁阻塞写测试

| Session-1 | Session-2 |
| --------- | --------- |
| 获取表 tb_book 的 Read Lock![read-lock1.png](/assets/数据库/mysql/myisam表锁/read-lock1.png)          |           |
| 当前 Session 可以查询该表记录![read-lock2.png](/assets/数据库/mysql/myisam表锁/read-lock2.png)          |  其他 Session 可以查询该表记录!![read-lock2.png](/assets/数据库/mysql/myisam表锁/read-lock2.png)         |
|  当前 Session 不能查询没有锁定的表![read-lock3.png](/assets/数据库/mysql/myisam表锁/read-lock3.png)         | 其他 Session 可以查询和更新没有锁定的表![read-lock4.png](/assets/数据库/mysql/myisam表锁/read-lock4.png)          |
|  当前 Session 插入和更新锁定的表错误![read-lock5.png](/assets/数据库/mysql/myisam表锁/read-lock5.png)         | 其他 Session 更新锁定的表会等待获得锁![read-lock6.png](/assets/数据库/mysql/myisam表锁/read-lock6.png)          |
|  当前 Session 释放锁![read-lock7.png](/assets/数据库/mysql/myisam表锁/read-lock7.png)         |  等待         |
|           |  Session 获得锁更新操作完成![read-lock8.png](/assets/数据库/mysql/myisam表锁/read-lock8.png)            |

## 读锁总结

- 当前用户：只能读加锁的表
- 其他用户：阻塞写加锁的表，可以读加锁的表，能够读写其他的表

# 6. 并发插入

MyISAM 读写是串行的，同样也可以支持查询和插入操作的并发进行。通过系统变量 concurrent_insert 控制并发插入的行为：

- 为 0 ：不允许并发插入
- 为 1：默认设置。如果表记录连续没有中断，则允许一个进程读表，另一个进程从**表尾插入**
- 为 2：无论 MyISAM 表记录有没有中断，都允许表尾并发插入

| Session-1 | Session-2 |
| --------- | --------- |
| 获取表 tb_book 的 Read Lock Local![read-local1.png](/assets/数据库/mysql/myisam表锁/read-local1.png)          |           |
| 当前 Session 插入和更新锁定的表错误![read-local2.png](/assets/数据库/mysql/myisam表锁/read-local2.png)           |  其他 Session 可以插入，但是更新等待![read-local3.png](/assets/数据库/mysql/myisam表锁/read-local3.png)          |
| 当前 Session 不能访问其他 Session 插入的记录![read-local4.png](/assets/数据库/mysql/myisam表锁/read-local4.png)           |           |
| 当前 Session 释放锁![read-local5.png](/assets/数据库/mysql/myisam表锁/read-local5.png)           |    等待       |
| 当前 Session 解锁后可以访问其他 Session 插入的记录![read-local6.png](/assets/数据库/mysql/myisam表锁/read-local6.png) 　|  Session-2 获得锁更新操作完成![read-local7.png](/assets/数据库/mysql/myisam表锁/read-local7.png)             |

# 7. 写锁阻塞读写测试

| Session-1 | Session-2 |
| --------- | --------- |
| 获取表 tb_book 的 Write Lock![write-lock1.png](/assets/数据库/mysql/myisam表锁/write-lock1.png)          |           |
| 当前 Session 不能查询没有锁定的表![write-lock5.png](/assets/数据库/mysql/myisam表锁/write-lock5.png)          |  其他 Session 可以查询和更新没有锁定的表![write-lock6.png](/assets/数据库/mysql/myisam表锁/write-lock6.png)         |
| 当前 Session 可以查询和更新该表记录![write-lock2.png](/assets/数据库/mysql/myisam表锁/write-lock2.png)          | 其他 Session 等待查询和更新该表记录![write-lock3.png](/assets/数据库/mysql/myisam表锁/write-lock3.png)![write-lock4.png](/assets/数据库/mysql/myisam表锁/write-lock4.png)          |
|  当前 Session 释放锁![write-lock7.png](/assets/数据库/mysql/myisam表锁/write-lock7.png)         |  等待         |
|                         |   Session 获得锁查询操作完成![write-lock8.png](/assets/数据库/mysql/myisam表锁/write-lock8.png)                                               |

## 写锁总结

- 当前用户：只能读写加锁的表，不能读写其他的表
- 其他用户：阻塞读写加锁的表，能够读写其他的表

# 8. 总结

简而言之，就是读锁会阻塞写，但是不会阻塞读。而写锁，则既会阻塞读，又会阻塞写。

此外，MyISAM 的读写锁调度是**写优先**，这也是 MyISAM 不适合做写为主的表的存储引擎的原因。因为写锁后，其他线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞。

