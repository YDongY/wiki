---
title: MySQL 锁总结
description: 
published: true
date: 2021-03-24T16:12:31.462Z
tags: 
editor: markdown
dateCreated: 2021-03-24T16:07:46.561Z
---

![事务与锁.png](/assets/数据库/mysql/锁总结/事务与锁.png)

# 锁的分类

- 表锁：锁定一张表

- 行锁：锁定一行记录

锁定粒度：表锁 > 行锁

加锁效率：表锁 > 行锁

冲突概率：表锁 > 行锁

并发性能：表锁 < 行锁

死锁：行锁会出现死锁，表锁不会出现死锁

> 在 InnoDB 存储引擎中既支持行锁又支持表锁，MyISAM 只支持表锁
{.is-info}


# 锁的模式

- **共享锁（行锁）**
- **排它锁（行锁）**
- **意向共享锁（表锁）**
- **意向排它锁（表锁）**
- **自增锁（表锁）**

> 对于 UPDATE、DELETE 和 INSERT 语句，InnoDB 会自动给涉及数据集加排他锁， 对于普通 SELECT 语句，InnoDB 不会加任何锁。
{.is-info}


## 共享锁（S）

共享锁又称为读锁，简称 S 锁，允许⼀个事务去读⼀⾏， 阻⽌其他事务获得相同数据集的排他锁，但是可以获得共享锁。

场景：事务都只能读取数据，不能修改。

加锁方式：

```sql
SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE
```

释放锁：

```sql
commit / rollback
```

## 排它锁（X）

排他锁又称为写锁，简称 X 锁。允许获得排他锁的事务更新数据， 阻⽌其他事务获得相同数据集的共享读锁和排他写锁

手动加锁：

```sql
SELECT * FROM table_name WHERE ... FOR UPDATE
```

## 意向共享锁（IS）

意向共享锁简称 IS 锁，由存储引擎自己维护，用户无法手动操作。表示事务给指定数据行加入共享锁之前必须先获取该表的 IS 锁

## 意向排它锁（IX）

意向排他锁简称 IX 锁，表示事务给指定数据行加入排他锁之前必须先获取该表的 IX 锁

> 在加⾏锁之前必须先获得表级意向锁， 否则等待 `innodb_lock_wait_timeout` 超时后根据 `innodb_rollback_on_timeout` 决定是否回滚事务。
> 通过意向共享锁和意向排它锁能够知道该表是否存在行锁，有利于加表锁的时候高效判断整个表是否存在锁。

## 自增锁

在 InnoDB 存储引擎中， 我们在设计表结构的时候， 通常会建议添加⼀列作为⾃增主键。 这⾥就会涉及⼀个特殊的锁： **`⾃增锁（ 即： AUTO-INC Locks）`** ， 它属于表锁的⼀种， 在 INSERT 结束后⽴即释放。

⾃增锁的使⽤过程中， 有⼀个核⼼参数 `innodb_autoinc_lock_mode` ，它有0、 1、 2 三个值。 一般默认值 2。

## InnoDB 锁关系矩阵

|              |  IS  |  IX  | AUTO_INC |  S   |  X   |
| :----------: | :--: | :--: | :------: | :--: | :--: |
|    **IS**    |  √   |  √   |    √     |  √   |  ×   |
|    **IX**    |  √   |  √   |    √     |  ×   |  ×   |
| **AUTO_INC** |  √   |  √   |    ×     |  ×   |  ×   |
|    **S**     |  √   |  ×   |    ×     |  √   |  ×   |
|    **X**     |  ×   |  ×   |    ×     |  ×   |  ×   |

# 行锁的疑问

锁是为了解决并发事务对资源竞争的问题，那么锁到底锁住的是什么？

行锁锁住的是主键索引，通过对一行记录的辅助索引加锁同样也会锁住主键索引。

# 行锁的算法

- **记录锁（Record Locks）**：单个⾏记录的锁（ 锁数据， 不锁 Gap）
- **间隙锁（Gap Locks）**：间隙锁， 锁定⼀个范围， 不包括记录本⾝（ 不锁数据， 仅仅锁数据前⾯的Gap）
- **临键锁（Next-Key Locks）**：同时锁住数据， 并且锁住数据前⾯的 Gap

## 测试数据

- id ：为 int 类型的主键索引
- name：为 int 类型的唯一索引

```sql
mysql> desc test;
+-------+------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+------+------+-----+---------+-------+
| id    | int  | NO   | PRI | NULL    |       |
| name  | int  | YES  | UNI | NULL    |       |
+-------+------+------+-----+---------+-------+
2 rows in set (0.00 sec)

mysql> select * from test;
+----+------+
| id | name |
+----+------+
|  1 |    1 |
|  4 |    4 |
|  8 |   88 |
| 10 |  100 |
+----+------+
4 rows in set (0.00 sec)
```

## 记录锁

通过**唯一索引或主键索引**进行等值查询加锁，此时加锁匹配到唯一记录，则采用的就是记录锁

| Session-1 | Session-2 |
| --------- | --------- |
| 开启事务![record-lock1.png](/assets/数据库/mysql/锁总结/record-lock1.png)          |   开启事务![record-lock1.png](/assets/数据库/mysql/锁总结/record-lock1.png)        |
| 更新 id=1 的数据不提交![record-lock2.png](/assets/数据库/mysql/锁总结/record-lock2.png)          | 更新 id=1 的数据发生阻塞![record-lock3.png](/assets/数据库/mysql/锁总结/record-lock3.png)          |
|           | 更新 id=4 的数据正常![record-lock4.png](/assets/数据库/mysql/锁总结/record-lock4.png)          |
| 只能查询到自己更新的数据，RR隔离级别解决了不可重复读![record-lock5.png](/assets/数据库/mysql/锁总结/record-lock5.png)         |   只能查询到自己更新的数据，RR 隔离级别解决了不可重复读![record-lock6.png](/assets/数据库/mysql/锁总结/record-lock6.png)        |
| commit 之后查询数据![record-lock7.png](/assets/数据库/mysql/锁总结/record-lock7.png)          |  commit 之后查询数据![record-lock7.png](/assets/数据库/mysql/锁总结/record-lock7.png)         |

结论：等值查询 RR 隔离级别下对**唯一索引或主键索引**进行加锁，只锁定单个记录其他事务无法更新操作。

## 间隙锁-REPEATABLE READ

- 在 RR 隔离级别下更新`(88,100)`的数据记录

| Session-1 | Session-2 |
| --------- | --------- |
| 开启事务![gap-lock1.png](/assets/数据库/mysql/锁总结/gap-lock1.png)          |  开启事务![gap-lock1.png](/assets/数据库/mysql/锁总结/gap-lock1.png)         |
| 更新 88<name<100 的 num 为 0![gap-lock2.png](/assets/数据库/mysql/锁总结/gap-lock2.png)          |           |
|           | 插入 name=99 的数据阻塞![gap-lock3.png](/assets/数据库/mysql/锁总结/gap-lock3.png)          |
|           | 更新 name=100 的数据阻塞![gap-lock5.png](/assets/数据库/mysql/锁总结/gap-lock5.png)         |
|           | 更新 name=88 的数据成功![gap-lock6.png](/assets/数据库/mysql/锁总结/gap-lock6.png)          |
|           | 在 88-100 范围之外插入 110、55 数据成功![gap-lock4.png](/assets/数据库/mysql/锁总结/gap-lock4.png)                                                                          |
|    提交       |                            提交                                     |

> 总结：对唯一索引 name 在 RR 隔离级别下，更新 88<name<100 的数据，锁定`(88,100]`区间，即：记录 88 到记录 100 ，不包含 88 的记录，此区间都无法修改和添加。
{.is-info}


- 在 RR 隔离级别下更新`[88,100]`的数据记录

| Session-1                                                    | Session-2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 开启事务![gap-lock11.png](/assets/数据库/mysql/锁总结/gap-lock11.png)       | 开启事务![gap-lock11.png](/assets/数据库/mysql/锁总结/gap-lock11.png)      |
| 更新 88<=name<=100 的 num 为 0![gap-lock66.png](/assets/数据库/mysql/锁总结/gap-lock66.png) |                                                              |
|                                                              | 插入 name=110 的数据阻塞![gap-lock22.png](/assets/数据库/mysql/锁总结/gap-lock22.png) |
|                                                              | 更新 name=130 的数据阻塞![gap-lock33.png](/assets/数据库/mysql/锁总结/gap-lock33.png) |
|                                                              | 插入 name=55 的数据阻塞![gap-lock44.png](/assets/数据库/mysql/锁总结/gap-lock44.png) |
|                                                              | 更新 name=44 的数据成功![gap-lock55.png](/assets/数据库/mysql/锁总结/gap-lock55.png) |
| 提交                                                         | 提交                                                         |

> 结果：锁定`[88,130]` 区间，即：记录 88 到记录 130 且包含记录 88 和记录 130，此区间都无法修改和添加。
{.is-info}


锁住 130 是因为在 RR 级别下，（ <=88 ）将从表（B+树）的第一个记录（ 1 ）开始比对，一直比对到第一个出现大于 8 的记录（ 10 ）为止。


## 间隙锁-READ COMMITTED

相同表数据，修改事务的隔离级别测试：

```sql
-- 修改事务隔离级别
mysql> set tx_isolation="READ-COMMITTED";

-- 查看事务隔离级别
mysql> select @@tx_isolation;
+----------------+
| @@tx_isolation |
+----------------+
| READ-COMMITTED |
+----------------+

mysql> select * from test;
+----+------+
| id | name |
+----+------+
|  1 |    1 |
|  4 |    4 |
|  8 |   88 |
| 10 |  100 |
+----+------+
4 rows in set (0.00 sec)
```

- **在 RC 隔离级别下查询** `(4,8)`的数据记录：不存在锁定状态，任意区间均可插入，任意记录均可修改，包括记录 4 和记录 8
- **在 RC 隔离级别下查询**`[4,8]`的数据记录：只会锁定记录 4 和记录 8，其他任意区间均可插入修改
- **在 RC 隔离级别下查询小于等于 4 的数据记录**：锁定小于等于 4 的所有行记录，但是不会锁定不存在行的区间，针对上面的数据来讲就是会锁定 1、4，但是仍然可以添加数据 3。

## 临键锁

临键锁是索引记录的锁定和索引记录之前的间隙上的间隙锁定的组合
$$
Next-Key  = GAP + Record
$$
假定索引包含值10、11、13、20 以及 50。此索引的可能的临键锁定包含以下间隔：

```sql
(negative infinity, 10]
(10, 11]
(11, 13]
(13, 20]
(20, 50]
(50, positive infinity)
```

假设现在需要更新大于 11 小于 13 的记录 name=0，id 为辅助索引：

```sql
update tablename set name=0 where id >= 11 and id <= 13;
```

锁定范围：`[11,20]` 锁住 20 是因为在 RR 级别下，（ <=13 ）将从表（B+树）的第一个记录（ 10 ）开始比对，一直比对到**第一个出现大于** 13 的记录（ 20 ）为止。此次过程和 GAP Lock 的第二个案例一致。

> 在 RR 隔离级别下，默认采用 Next-Key Locks
{.is-info}


# 其他情况

## Next-Key lock 优化为 Record lock

RR 隔离级别下采用主键（唯一约束）且返回的记录只有一条时，此时不会出现幻读，即临键锁降为记录锁。

## 非唯一索引的等值查询

不存在主键和唯⼀索引时，系统自己生成主键，当进行等值查询时，要访问所有的记录 （索引），来判断主键对应的列是否符合条件。即需要扫描所有主键，则会将所有记录（索引）都锁住 。形成了表锁的效果。

即：在 RR 的隔离级别下，当查询的列没有索引时，会锁住所有记录

## 假如 RR 没有 Gap Lock

存在记录操作如下：

```sql
# 表记录
+----+------+
| id | name |
+----+------+
|  1 |    1 |
|  4 |    4 |
|  8 |    8 |
| 10 |   10 |
+----+------+
```

|            Session-1             |                Session-2                |
| :------------------------------: | :-------------------------------------: |
|              begin               |                  begin                  |
| `delete from table where id<=10` |                                         |
|                                  | `insert into table(id,name) value(6,6)` |
|                                  |                 commit                  |
|              commit              |                                         |

如果在 RR 级别下，且没有 Gap 锁 ，则此时 Session-1 锁住的是 1， 4， 8， 10 （记录锁），并标记为删除 ，同时 Session-2 可以插入记录 6 。即此时表中的记录为 6。 但是 Log 中记录的是 insert 6 delete <=10 顺序提交，如果此时有 slave 机器进行同步，该表中的记录为空。 此时主从机器上的数据不一致 。

当有了 Gap Lock 后， Session-2 的 insert 6; 需要等待 Session-1 的 delete<=10 执行完成后才能执行，此时的 Log 为 delete<=7 insert 6 ，也就不会有数据不一致的问题（符合隔离性要求）

# 加锁行为

分析 InnoDB 存储引擎下不同索引在 RR 隔离级别下的是个场景：

- 主键 + RR
- 唯一键 + RR
- 非唯一键 + RR
- 无索引 + RR

## 主键 + RR

![主键+rr.png](/assets/数据库/mysql/锁总结/主键+rr.png)

假设条件：

- id 为主键索引。
- `update t1 set name=‘XX’ where id=10;`

加锁⾏为：

- 仅在 `id=10` 的主键索引记录上加 X锁。

## 唯⼀键 + RR

![唯一键+rr.png](/assets/数据库/mysql/锁总结/唯一键+rr.png)

假设条件：

- `update t1 set name='XX' where id=10；`
- id 为唯⼀索引。

加锁⾏为：

- 先在唯⼀索引 id 上加 id = 10 的 X 锁。
- 再在 id = 10 的主键索引记录上加 X 锁， 若 id = 10 记录不存在， 那么加间隙锁。

## ⾮唯⼀键 + RR

![非唯一键+rr.png](/assets/数据库/mysql/锁总结/非唯一键+rr.png)

假设条件是：

- `update t1 set name=‘XX’ where id=10。 `
- id 为⾮唯⼀索引

加锁⾏为：

- 先通过 id=10 在 `key(id)` 上定位到第⼀个满⾜的记录， 对该记录加 X 锁， ⽽且要在 `(6,c)~(10,b)` 之间加上 Gap lock， 为了防⽌幻读。 然后在主键索引 name 上加对应记录的 X 锁；
- 再通过 id=10 在 `key(id)` 上定位到第⼆个满⾜的记录， 对该记录加 X 锁， ⽽且要在 `(10,b)~(10,d)` 之间加上 Gap lock， 为了防⽌幻读。 然后在主键索引 name 上加对应记录的 X 锁；
- 最后直到 id=11 发现没有满⾜的记录了， 此时不需要加 X 锁， 但要再加⼀个 Gap lock： (`10,d)~ (11,f)`。

## ⽆索引 + RR

![无索引+rr.png](/assets/数据库/mysql/锁总结/无索引+rr.png)

假设条件是：

- `update t1 set name=‘XX’ where id=10；`
-  id 列⽆索引。

加锁⾏为：

- 表⾥所有⾏和间隙均加 X 锁。

## 死锁

![死锁.png](/assets/数据库/mysql/锁总结/死锁.png)

在 InnoDB 中产生死锁的条件：

- 互斥条件： ⼀个资源每次只能被⼀个进程使⽤；
- 请求与保持条件： ⼀个进程因请求资源⽽阻塞时， 对已获得的资源保持不放；
- 不剥夺条件： 进程已获得的资源， 在没使⽤完之前， 不能强⾏剥夺；
- 循环等待条件： 多个进程之间形成的⼀种互相循环等待资源的关系。

资源竞争：

session1 ⾸先拿到 id=1 的锁， session2 同时拿到了 id=5 的锁后， 两者分别想拿到对⽅持有的锁， 于是产⽣死锁。

在发⽣死锁时， InnoDB 存储引擎会⾃动检测， 并且会⾃动回滚代价较⼩的事务来解决死锁问题，但是一般都是人为手动解决。

**如何避免死锁的产⽣呢？**

- 加锁顺序⼀致；
- 尽量基于 primary 或 unique key 更新数据。
- 单次操作数据量不宜过多， 涉及表尽量少。
- 减少表上索引， 减少锁定资源。
- 相关⼯具： pt-deadlock-logger。

