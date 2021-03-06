---
title: 理解 ACID 原则和事务隔离级别
description: 
published: true
date: 2021-03-24T15:38:02.639Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:37:14.480Z
---

# 什么是事务

数据库事务(Database Transaction) ，是指作为单个逻辑工作单元执行的一系列操作，要么全部执行，要么全部不执行。

一个数据库事务通常包含对数据库进行读或写的一个操作序列。它的存在包含有以下两个目的：

- 为数据库提供一个从失败恢复正常状态的方法，同时提供了数据库即使在异常状态下仍然能保持一致性的方法。
- 当多个应用程序并发访问数据库时，可以在这些应用程序之间提供一个隔离的方法，以防止彼此的操作相互干扰。

当然并不是所有的数据库都支持事务，确切说应该是存储引擎。在`InnoDB`存储引擎中，事务一般有四个属性：原子性、一致性、隔离性、持久性。简称：ACID 原则。接下来我们举例逐个分析：

- 原子性：顾名思义原子就不可在分的，事务作为一个整体，对数据库的执行要么成功，要么失败。
- 一致性：事务从一个一致状态转换到另一个一致状态。
- 隔离性：多个事务并发执行，事务之间的执行互不干扰。
- 持久性：一旦一个事务提交，它对数据库的操作将永久保存到数据库当中。

**举例：**

我们用一个转账的案例结合每个性质分析，例如：账户A向账户B转账，主要分为一下几个步骤：

1. 从A账号中把余额读出来（500）。
2. 对A账号做减法操作（500-100）。
3. 把结果写回A账号中（400）。
4. 从B账号中把余额读出来（500）。
5. 对B账号做加法操作（500+100）。
6. 把结果写回B账号中（600）。

**原子性**

保证６个步骤要么全部执行，要么全部不执行，如果失败了就把事务回滚到转账的初始状态。比如：在执行到第五步的之后，账户B突然注销了找不到了，此时账户A的钱也扣了，就必须事务回滚到原来各自的状态也就是A的余额500。

**一致性**

在转账之前A和B的账户共有500+500=1000，而转账成功之后，A和B的账户是400+600=1000，就是数据的状态在执行该事务操作之后从一个状态改变到了另外一个状态

**隔离性**

在A给B转账过程中，只要事务没有提交，A和B的账户余额不会存在变化。但是此时账户C也在向账户B转账，最终两个事务提交之后账户B的余额应该是账户A转账的金额加上账户C转账的金额。也就是说多个事务之间不会相互影响，至于C给B转账的时候获取B的余额是已经加了A给B转账的余额还是没加，这个和事务的隔离级别有关系。

**持久性**

一旦转账成功（事务提交），两个账户的里面的钱就会真的发生变化（会把数据写入数据库做持久化保存）！

# 事务的隔离级别

- Read-uncommitted(读未提交)：最低级别，以上情况均无法保证。
- Read-committed（读已提交）：**可避免脏读**情况发生
- Repeatable-read（可重复读，默认）：**可避免脏读、不可重复读情况的发生**。**不可以避免幻读**
- Serializable：**可避免脏读、不可重复读、幻读情况的发生**。(串行，不仅有 read、write 锁还有r ange lock 范围锁（没有 where 锁全表，有 where 锁 where 范围），对一张表的所有增删改操作必须顺序执行，性能最差)

|     **隔离级别**     | **脏读** | **不可重复读** | **幻读** |
| :------------------: | :------: | :------------: | :------: |
| **Read-uncommitted** |    √     |       √        |    √     |
|  **Read-committed**  |    ×     |       √        |    √     |
| **Repeatable-read**  |    ×     |       ×        |    √     |
|   **Serializable**   |    ×     |       ×        |    ×     |

## Read uncommitted

作用：**所有事务都可以看到其他未提交事务的执行结果**

例子:

又到月底了，小明的老婆要准备给小明发生活费了，小明的老婆给小明打了 500 块，但该事务并没有提交，而此时小明正好在查余额，发现是 550 块，高兴的差点蹦了起来。天有不测风云，突然小明的老婆发现多打了 50 块，于是回滚事务，修改金额，然后将事务提交，最后小明空欢喜一场。**这个过程中小明读取到了老婆未提交的金额 550，产生了脏读。**

![mysql-事务.png](/assets/数据库/mysql/事务和隔离级别/mysql-事务.png)

## Read committed

作用：**一个事务只能看见已经提交事务所做的改变**

例子:

某个夜黑风高的夜晚，小明丰富的夜生活开始了，小明拿着工资卡去消费，`pos`机读取卡的信息的时候有 500，而此时小红也正好在网上转账，把小明工资卡的 500 元转到另一账户，并小明之前提交了事务，当小明扣款时，系统检查到小明的工资卡已经没有钱，扣款失败，小明十分纳闷，明明卡里有钱，为什么会说余额不足，出现上述情况，即我们所说的不可重复读，两个并发的事务，“事务1：小红网上转账”、“事务2：小明消费”，事务1事先读取了数据，紧接了更新了数据，然后事务2查询数据，之后事务1优先提交了事务，而事务2再次读取该数据时，数据已经发生了改变，当隔离级别设置为 Read committed 时，**避免了脏读，但是可能会造成不可重复读**

![mysql-事务2.png](/assets/数据库/mysql/事务和隔离级别/mysql-事务2.png)

## Repeatable read

- 备注
  1. `InnoDB`存储引擎的默认隔离级别
  2. 从原理上看，可重复读是靠`MVCC`（多版本并发控制）保证的，该模式下，保证事务只能读取到当前事务开启之前已经提交的事务的修改以及当前事务本身对数据的修改
  3. 可重复读隔离级别无法避免幻读

由于幻读的案例比较特殊，现在暂时没法通过一个例子画图表示，具体过程可以查看后面的会话比较。

- **Serializable(串行)**

作用：最高级别，防止上述3种情况，事务串行执行，慎用这是最高的隔离级别，它通过强制事务排序，使之不可能相互冲突，从而避免了脏读、不可重复读、幻读。简言之，它是在每个读的数据行上加上共享锁。在这个级别，可能导致大量的超时现象和锁竞争，并发性能最差,在分布式事务中可能会被用到。

# 测试案例

- 环境准备

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `NAME` varchar(20),
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

insert into user values(1,'zhangsan');
insert into user values(2,'lisi');
insert into user values(3,'wangwu');

mysql> select * from user;
+----+----------+
| id | NAME     |
+----+----------+
|  1 | zhangsan |
|  2 | lisi     |
|  3 | wangwu   |
+----+----------+
3 rows in set (0.00 sec)

--查看当前事务隔离级别,当前版本 8.0 ,旧版本可能是 SELECT @@tx_isolation;
mysql> show variables like '%iso%';
+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.00 sec)
```

## 脏读案例

测试条件：

- `InnoDB`储存引擎
- 两个会话客户端
- `Read-uncommitted`隔离级别

| Session-1                                                    | Session-2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![脏读1.png](/assets/数据库/mysql/事务和隔离级别/脏读1.png)                                                     | ![脏读2.png](/assets/数据库/mysql/事务和隔离级别/脏读2.png) |
| ![脏读3.png](/assets/数据库/mysql/事务和隔离级别/脏读3.png) | ![脏读4.png](/assets/数据库/mysql/事务和隔离级别/脏读4.png) |
| ![脏读5.png](/assets/数据库/mysql/事务和隔离级别/脏读5.png) |                                                              |
|                                                              | ![脏读6.png](/assets/数据库/mysql/事务和隔离级别/脏读6.png) |
| ![脏读7.png](/assets/数据库/mysql/事务和隔离级别/脏读7.png) | ![脏读8.png](/assets/数据库/mysql/事务和隔离级别/脏读8.png) |

> 脏读：Session-1 事务中未提交前修改 name 字段值，在 Session-2 事务未提交前可以查看到
{.is-info}


## 不可重复读案例

测试条件：

- `InnoDB`储存引擎
- 两个会话客户端
- `Read-committed`隔离级别

| Session-1                                                    | Session-2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![不可重复读1.png](/assets/数据库/mysql/事务和隔离级别/不可重复读1.png) | ![不可重复读2.png](/assets/数据库/mysql/事务和隔离级别/不可重复读2.png) |
| ![不可重复读3.png](/assets/数据库/mysql/事务和隔离级别/不可重复读3.png) | ![不可重复读4.png](/assets/数据库/mysql/事务和隔离级别/不可重复读4.png) |
| ![不可重复读5.png](/assets/数据库/mysql/事务和隔离级别/不可重复读5.png) |                                                              |
|                                                              | ![不可重复读6.png](/assets/数据库/mysql/事务和隔离级别/不可重复读6.png) |
| ![不可重复读7.png](/assets/数据库/mysql/事务和隔离级别/不可重复读7.png) |                                                              |
|                                                              | ![不可重复读8.png](/assets/数据库/mysql/事务和隔离级别/不可重复读8.png) |
|                                                              | ![不可重复读9.png](/assets/数据库/mysql/事务和隔离级别/不可重复读9.png) |

> 不可重复读：Session-1 事务中未提交的数据修改时，Session-2 事务未提交前无法查看（避免了脏读），当 Session-1 事务提交后，Session-2 事务在未提交前，查看到 Session-1 提交的修改。（造成了不可重复读，一个事务执行过程中两次读取的数据不一样）
{.is-info}


## 幻读案例

测试条件：

- `InnoDB`储存引擎
- 两个会话客户端
- `Repaetable read`隔离级别

| Session-1                                                    | Session-2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![幻读1.png](/assets/数据库/mysql/事务和隔离级别/幻读1.png) | ![幻读2.png](/assets/数据库/mysql/事务和隔离级别/幻读2.png) |
| ![幻读3.png](/assets/数据库/mysql/事务和隔离级别/幻读3.png) | ![幻读4.png](/assets/数据库/mysql/事务和隔离级别/幻读4.png) |
| ![幻读5.png](/assets/数据库/mysql/事务和隔离级别/幻读5.png) |                                                              |
|                                                              | ![幻读6.png](/assets/数据库/mysql/事务和隔离级别/幻读6.png) |
| ![幻读7.png](/assets/数据库/mysql/事务和隔离级别/幻读7.png) |                                                              |
|                                                              | ![幻读8.png](/assets/数据库/mysql/事务和隔离级别/幻读8.png) |
| ![幻读9.png](/assets/数据库/mysql/事务和隔离级别/幻读9.png) |                                                              |
|                                                              | ![幻读10.png](/assets/数据库/mysql/事务和隔离级别/幻读10.png) |
|                                                              | ![幻读11.png](/assets/数据库/mysql/事务和隔离级别/幻读11.png) |

> 幻读：在`Repeatable read`隔离级别下，Session-1 无论在事务提交前还是提交后，在 Session-2 事务中始终保持的都是开始事务之前的数据状态，从而避免了脏读，不可重复读，但是当 Session-1 进行插入一条数据时，Session-2 同样无法看到，但是此时 Session-2 同样也插入一条数据，就会处于阻塞状态。感觉就像是，明明没有数据，为什么不能插入呢，从而造成了幻读。当然对于删除也是同样的，可能会出现删除记录不存在。
{.is-info}


不过，`InnoDB`默认是行锁，也就是锁只会锁住当前的一行，如果 Session-2 执行`insert into user values(5,'xxx')`就可以成功执行。

# 相关参考

- http://www.hollischuang.com/archives/898