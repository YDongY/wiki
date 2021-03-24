---
title: InnoDB 行锁
description: 
published: true
date: 2021-03-24T15:43:57.518Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:43:21.847Z
---

# 1. 查询 InnoDB 锁争用情况

```sql
mysql> show status like 'innodb_row_lock%';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| Innodb_row_lock_current_waits | 0     |
| Innodb_row_lock_time          | 22432 |
| Innodb_row_lock_time_avg      | 1319  |
| Innodb_row_lock_time_max      | 3005  |
| Innodb_row_lock_waits         | 17    |
+-------------------------------+-------+
5 rows in set (0.03 sec)
```

- `Innodb_row_lock_current_waits`：当前正在等待锁定的数量
- `Innodb_row_lock_time`：从系统启动到现在锁定总时间长度
- `Innodb_row_lock_time_avg`：每次等待所花平均时长
- `Innodb_row_lock_time_max`：从系统启动到现在等待最长的一次所花的时间
- `Innodb_row_lock_waits`：系统启动后到现在总共等待的次数

当 `Innodb_row_lock_waits` 和 `Innodb_row_lock_time_avg` 值比较大这说明锁的争用比较严重。

# 2. InnoDB 行锁模式及加锁方式

行锁：开销大、加锁慢、会出现死锁、锁定粒度最小、发生锁冲突的概率最低、并发度也最高。

InnoDB 实现了以下两种类型的行锁。

- 共享锁（S）：又称为读锁，简称 S 锁，共享锁就是多个事务对于同一数据可以共享一把锁，都能访问到数据，但是只能读不能修改。同时阻止其他事务获得相同数据集的排它锁
- 排他锁（X）：又称为写锁，简称 X 锁，排他锁就是不能与其他锁并存，如一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁（包括共享锁和排他锁），但是获取排他锁的事务是可以对数据就行读取和修改。
- 意向共享锁（IS）：简称 IS 锁，由存储引擎自己维护，用户无法手动操作。表示事务给指定数据行加入共享锁之前必须先获取该表的 IS 锁
- 意向排他锁（IX）：简称 IX 锁，表示事务给指定数据行加入排他锁之前必须先获取该表的 IX 锁

意向锁是 InnoDB 自己加的，不需要用户干预，对于 UPDATE、DELETE 和 INSERT 语句，InnoDB 会自动给涉及数据集加排他锁（X），对于普通 SELECT 语句，InnoDB 不会加任何锁。

可以通过以下语句显示给记录集加共享锁或排他锁 。

```sql
共享锁（S）：SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE
排他锁（X）：SELECT * FROM table_name WHERE ... FOR UPDATE
```

# 3. 行锁案例

准备相关测试表和数据，注意表的存储引擎为 InnoDB：

```sql
create table test_innodb_lock(
	id int(11),
	name varchar(16),
	sex varchar(1)
)engine = innodb default charset=utf8;

insert into test_innodb_lock values(11,'100','1');
insert into test_innodb_lock values(3,'3','1');
insert into test_innodb_lock values(4,'400','0');
insert into test_innodb_lock values(5,'500','1');
insert into test_innodb_lock values(6,'600','0');
insert into test_innodb_lock values(7,'700','0');
insert into test_innodb_lock values(8,'800','1');
insert into test_innodb_lock values(9,'900','1');
insert into test_innodb_lock values(1,'200','0');

create index idx_test_innodb_lock_id on test_innodb_lock(id);
create index idx_test_innodb_lock_name on test_innodb_lock(name);
```

创建完成，我们看一下表结构和表数据，方便后面操作查看：

```sql
mysql> desc test_innodb_lock;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  | MUL | NULL    |       |
| name  | varchar(16) | YES  | MUL | NULL    |       |
| sex   | varchar(1)  | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.00 sec)


mysql> select * from test_innodb_lock;
+------+------+------+
| id   | name | sex  |
+------+------+------+
|   11 | 100  | 1    |
|    3 | 3    | 1    |
|    4 | 400  | 0    |
|    5 | 500  | 1    |
|    6 | 600  | 0    |
|    7 | 700  | 0    |
|    8 | 800  | 1    |
|    9 | 900  | 1    |
|    1 | 200  | 0    |
+------+------+------+
9 rows in set (0.00 sec)
```

## 3.1 共享锁案例

| Session-1 | Session-2 |
| --------- | --------- |
|  ![innodb-lock1.png](/assets/数据库/mysql/innodb行锁/innodb-lock1.png)         |     ![innodb-lock1.png](/assets/数据库/mysql/innodb行锁/innodb-lock1.png)      |
|  ![innodb-lock2.png](/assets/数据库/mysql/innodb行锁/innodb-lock2.png)         |           |
|           |  ![innodb-lock2.png](/assets/数据库/mysql/innodb行锁/innodb-lock2.png)         |
| ![innodb-lock3.png](/assets/数据库/mysql/innodb行锁/innodb-lock3.png)          |           |
|           |  ![innodb-lock4.png](/assets/数据库/mysql/innodb行锁/innodb-lock4.png)         |
| ![innodb-lock5.png](/assets/数据库/mysql/innodb行锁/innodb-lock5.png)          |                                               |

## 3.2 排它锁案例

| Session-1 | Session-2 |
| --------- | --------- |
| ![innodb-lock11.png](/assets/数据库/mysql/innodb行锁/innodb-lock11.png)         |  ![innodb-lock11.png](/assets/数据库/mysql/innodb行锁/innodb-lock11.png)         |
| 当前 Session 对 id=1 的记录加 for update 的共享锁 ![innodb-lock22.png](/assets/数据库/mysql/innodb行锁/innodb-lock22.png)       |           |
|           | 其他 Session 可以查询该记录，但是不会对该记录加共享锁，等待获得锁![innodb-lock33.png](/assets/数据库/mysql/innodb行锁/innodb-lock33.png)          |
| 当前 Session 可以对锁定的记录进行更新操作，更新后释放锁![innodb-lock44.png](/assets/数据库/mysql/innodb行锁/innodb-lock44.png)          |           |
|           |  其他 Session 获得锁，得到其他 Session 提交的记录![innodb-lock55.png](/assets/数据库/mysql/innodb行锁/innodb-lock55.png)         |

