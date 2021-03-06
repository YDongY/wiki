---
title: MySQL 基本命令
description: 
published: true
date: 2021-03-24T15:21:18.253Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:18:44.644Z
---

![sql基础.png](/assets/数据库/mysql/sql基础.png)

# 帮助文档

> 命令很多，不常用就遗忘，使用帮助文档是根本，所以只需要记住 ? 命令:stuck_out_tongue_winking_eye:
{.is-info}

```sql
? contents;
? data types;
? int;
? auto_increment;
? show;
```

# 实体完整性

> 表中的一行代表一个实体
> 作用：标识每一行数据不重复。行级约束
{.is-info}

## 主键约束

### PRIMARY KEY

> 每一个表中要有一个主键，数据唯一，且不能为 NULL
{.is-info}


```sql
--create table 表名(字段名1 数据类型 primary key,字段2 数据类型..)
CREATE TABLE person(id BIGINT PRIMARY KEY,name VARCHAR(50));
--create table 表名(字段名1 数据类型,字段2 数据类型,primary key(字段名))
CREATE TABLE person2(id BIGINT,NAME varchar(20),PRIMARY KEY(id));
```

### 联合主键

> 多个字段同时相同，才会违反联合组件约束

```sql
--create table 表名(字段名1 数据类型,字段2 数据类型,primary key(字段名,字段名))
CREATE TABLE person3(id BIGINT,NAME varchar(20),PRIMARY KEY(id,name));
```

- 添加主键

```sql
-- ALTER TABLE 表名 add CONSTRAINT PRIMARY KEY(字段名...);
ALTER TABLE person4 add CONSTRAINT PRIMARY KEY(id);
```

## 唯一约束

> `UNIQUE`：标识每一行数据不重复。可以为空值

```sql
 CREATE TABLE person5(id BIGINT UNIQUE,name VARCHAR(20));
```

## 自增长列

> `AUTO_INCREMENT`：指定列的数据自动增长，即数据物理删除之后，还是会从删除的序号继续往下

```sql
CREATE TABLE student(id INT PRIMARY KEY AUTO_INCREMENT,name VARCHAR(20) UNIQUE);
```

# 域完整性

> 限制单元格的数据正确
{.is-info}

- 非空约束：`not null`

```sql
CREATE TABLE student(id INT NOT NULL,name VARCHAR(20) UNIQUE);
```

- 默认值约束：`detault`

```sql
CREATE TABLE student(id INT NOT NULL,gender VARCHAR(20) DEFAULT 'mela' );
```

# 参照完整性

> 表与表之前的关系，两个表必要是`InnonDB`类型，主键和外键类型要一致
{.is-info}

## 一对多，外键

```sql
CREATE TABLE `student` (
  `id` int(11) NOT NULL,
  `name` varchar(50) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `score` (
  `sid` int(11) DEFAULT NULL,
  `score` int(11) DEFAULT NULL,
  KEY `sc_st_fk` (`sid`),
  CONSTRAINT `sc_st_fk` FOREIGN KEY (`sid`) REFERENCES `student` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

- 添加表约束

```sql
ALTER TABLE score ADD CONSTRAINT sc_st_fk FOREIGN KEY(sid) REFERENCES student(id);
```

## 多对多

```sql
--学生表
CREATE TABLE teacher(id INT PRIMARY KEY,name VARCHAR(50));
--老师表
CREATE TABLE student(id INT PRIMARY KEY,name VARCHAR(50));
--中间表
CREATE TABLE tea_stu_rel(tid INT,sid INT);
--中间表外键约束
ALTER TABLE tea_stu_rel ADD CONSTRAINT FOREIGN KEY(tid) REFERENCES teacher(id);
--中间表外键约束
ALTER TABLE tea_stu_rel ADD CONSTRAINT FOREIGN KEY(sid) REFERENCES stu(id);
```

```sql
--创建表的同时增加外键约束
CREATE TABLE tea_stu_rel(
tid INT,
sid INT,
CONSTRAINT FOREIGN KEY(tid) REFERENCES teacher(id),
CONSTRAINT FOREIGN KEY(sid) REFERENCES student(id)
);
```

# DDL

```sql
-- 创建表

CREATE TABLE stu(
  id int,
  name varchar(255),
  age int,
  email varchar(255)
);

-- 添加列
alert table stu add score int;
/*eg:alter table 表名 add 字段名 字段类型*/


-- 修改字段类型
alter table stu modify id bigint;
/*eg:alter table 表名 modify 字段名 字段类型*/


-- 删除一列
alter table stu drop nums;
/*eg:alter table 表名 drop 列名*/


-- 修改表名
rename table emplyee to employee;
/*eg:rename table 表名 to 新表名*/


-- 查看创建表
show create table stu;
--show create 表名
CREATE TABLE `stu` (
  `id` bigint(20) DEFAULT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `score` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8


-- 修改表的字符集
alter table 表名 character set 字符集;
--alter table employee character set gbk;


-- 修改列名
alter table 表名 change 列名 新列名 类型;
--alter table employee change name username varchar(20);


-- 删除表
drop table employee;
--drop table 表名
```

# DML

```sql
-- 插入数据
insert into stu(id,name,age,email,score)values(2,'lisi',18,'example@example.com',null);
--insert 表名(列名1,列名2...) values(列值1,列值2)


-- 批量插入
insert into stu(id,name,age,email,score) values(2,'lisi',18,'example@example.com',null)(3,'wangwu',18,'example@example.com',30);
--insert 表名(列名1,列名2...) values(列值1,列值2),(列值1,列值2)...


-- 更新数据
update stu set score=60 where id=2;
--update 表名 set 列名＝列值,列名＝列值...
update stu set age=20,score=60 where id=3;
--update 表名 set 列名＝列值,列名＝列值... [where] 字段名=字段值
update stu set age=20+1 where id=3;


-- 删除数据
-- 删除表中的数据，表结构还在
delete from stu where id=2;
--delete from 表名 [条件]


-- 删除所有数据,删除是把表直接drop掉，然后在创建一个同样的新表
truncate table stu;
```

# DQL

## 查询所有

```sql
--select * from 表名
select * from stu;

-- 查询指定列
SELECT name,age FROM stu;
```

## 条件查询


```sql
--查询性别是男并且年龄大于18
SELECT * FROM stu where gender='male' and age>18;


-- `between ...  and ...`
--查询年龄在20-22
SELECT * FROM stu WHERE age BETWEEN 20 and 22;
SELECT * FROM stu WHERE age>=20 and age<=22;


-- `in`
--查询id为1,2,3的记录
SELECT * FROM stu where id in (1,2,3);


-- `is null、is not null`
--查询邮箱为空
SELECT * FROM stu where email is NULL;
--查询姓名不为空
SELECT * FROM stu where name is NOT NULL;


-- `and`
--查询性别为男，而且邮箱不为空的记录
SELECT * FROM stu where gender='male' and email is NOT NULL;


-- `or`
--查询id=1或者名字是wangwu的记录
SELECT * FROM stu where id=1 or name='wangwu';
```

## 模糊查询

- `_`：表示单个字符
- `%`：任意0-个字符

```sql
--查询姓名有5个字母组成的记录
SELECT name FROM stu WHERE name LIKE '____';

--查询姓名有5个字母组成，并且最后一个字母为s
SELECT name FROM stu WHERE name LIKE '____s';

--查询名字以 y 开头的记录
SELECT name FROM stu WHERE name LIKE 'y%';

--查询姓名中第2个字母为 d 的记录
SELECT name FROM stu WHERE name LIKE '_d%';

--查询姓名包含 s 的记录
SELECT name FROM stu WHERE name LIKE '%s%';
```

## 字段控制查询

```sql
--去掉重复的名字
SELECT DISTINCT name FROM stu;


-- 运算，必须是数值类型
--字段年龄加上分数值
SELECT *,age+score FROM stu;
--如果字段为NULL,则设置为0
SELECT *,IFNULL(age,0)+IFNULL(score,0) FROM stu;


-- 起别名：`as`
--起别名为total
SELECT *,IFNULL(age,0)+IFNULL(score,0) as total FROM stu;
```

## 排序(order by)

- `ASC`：从小到大，默认
- `DESC`：从大到小

```sql
--查询按照年龄由小到大
SELECT * FROM stu ORDER BY age;
--查询按照id由大到小
SELECT * FROM stu ORDER BY age DESC;
--查询按照age降序，age相同的按照score升序
SELECT * FROM stu ORDER BY age DESC;,score；
```

## 聚合函数

```sql
-- `COUNT()`
-- 统计不为NULL的记录行数
--统计表中记录
SELECT COUNT(*) FROM stu;
--查询年龄大于20的人数
SELECT COUNT(*)FROM stu WHERE age > 20;
--查询有邮箱，和有手机号的记录人数
SELECT COUNT(email),COUNT(phone) FROM stu;


-- `MAX()`
-- 计算指定列最大值，如果指定列是字符串类型，那么使用字符串排序运算。
--计算年龄最大的
SELECT MAX(age) FROM stu;


-- `MIN()`
-- 计算指定列最小值，如果指定列是字符串类型，那么使用字符串排序运算。
--计算分数最小的
SELECT MIN(score) FROM stu;


-- `SUM()`
-- 计算指定列的数值和，如果指定列类型不是数值类型，那么计算结果为0。
--查询所有年龄和以及分数值和
SELECT SUM(age),SUM(score) FROM stu;
--查询年龄加上分数的和
SELECT SUM(IFNULL(age,0)+IFNULL(score,0)) FROM stu;


-- `AVG()`
-- 计算指定列的平均值，如果指定列类型不是数值类型，那么计算结果为0。
--查询所有年龄平均值以及分数平均值
SELECT AVG(age),AVG(score) FROM stu;
```

## 分组查询

> 将查询结果按照一个或多个字段进行分组，字段值相同的为一组
{.is-info}

```sql
--按照性别分组
SELECT * FROM stu GROUP BY gender;　--MySQL5.7有问题

-- `GROUP_CONCAT`
-- 表示分组之后，根据分组结果，使用`GROUP_CONCAT()`来放置一组的某字段的值的集合
--按照性别分组,查看每个分组包含那些人
SELECT gender,GROUP_CONCAT(name) FROM stu GROUP BY gender;
+--------+----------------------+
| gender | GROUP_CONCAT(name)   |
+--------+----------------------+
| female | wangwu,lisi          |
| male   | zhangsan,lisi,ydongy |
+--------+----------------------+


-- `group by`+聚合函数
--查询每个性别年龄总和
SELECT gender,GROUP_CONCAT(age),SUM(age) FROM stu GROUP BY gender;
+--------+-------------------+----------+
| gender | GROUP_CONCAT(age) | SUM(age) |
+--------+-------------------+----------+
| female | 22,26             |       48 |
| male   | 18,20,22          |       60 |
+--------+-------------------+----------+

--查询不同性别的姓名以及年龄大于20的人数
SELECT gender,GROUP_CONCAT(age),GROUP_CONCAT(name) FROM stu WHERE age > 20 GROUP BY gender;
+--------+-------------------+--------------------+
| gender | GROUP_CONCAT(age) | GROUP_CONCAT(name) |
+--------+-------------------+--------------------+
| female | 22,26             | wangwu,lisi        |
| male   | 22                | ydongy             |
+--------+-------------------+--------------------+

-- `group by`+`having`
-- 用于分组查询之后指定一些条件输出查询结果
--查询每个性别年龄总和大于50的
SELECT gender,GROUP_CONCAT(age),SUM(age) FROM stu GROUP BY gender HAVING SUM(age)>50;
+--------+-------------------+----------+
| gender | GROUP_CONCAT(age) | SUM(age) |
+--------+-------------------+----------+
| male   | 18,20,22          |       60 |
+--------+-------------------+----------+
```

### having 与 where 区别

- `having`在分组之后过滤，`where`在分组之前过滤
- `having`后面可以使用分组函数(聚合函数)，`where`后面不可以使用聚合函数
- `where`是对分组前记录的条件过滤，不满足过滤条件的不会参与分组，`having`是对分组后的数据约束

### limit

```sql
--获取查询结果前三条
SELECT * FROM stu LIMIT 0,3;
--查询第二个三条记录
SELECT * FROM stu LIMIT 3,3;
```

### 书写顺序

![sql书写顺序.png](/assets/数据库/mysql/sql书写顺序.png)

### 执行顺序

![sql执行顺序.png](/assets/数据库/mysql/sql执行顺序.png)

## 多表查询

```sql
--`UNION`：合并时去除重复
SELECT * FROM A UNION SELECT * FROM B;


-- `UNION ALL`：合并时不去除重复
SELECT * FROM A UNION ALL SELECT * FROM B;
```

### 连接查询

- 笛卡尔积

```sql
SELECT * FROM student,teacher;
```

- 内连接

  - 等值连接

  ```sql
  SELECT * FROM score as sc INNER JOIN student as st on st.id=sc.sid;
  ```

  - 非等值连接

  ```sql
  SELECT st.name,sc.score FROM score as sc JOIN student as st on st.id=sc.sid where sc.score>60;
  ```

  - 自连接
  ```sql
  SELECT * FROM score as sc,student as st WHERE st.id=sc.sid;
  ```

- 外连接

  - 左连接：左边表数据全部查询出来，右边表只查询满足条件的数据，条件不满足，用null补充

  ```sql
  SELECT * FROM score as sc LEFT JOIN student as st on st.id=sc.sid;
  ```

  - 右连接：右边表数据全部查询出来，左边表只查询满足条件的数据，条件不满足，用null补充

  ```sql
  SELECT * FROM score as sc RIGHT JOIN student as st on st.id=sc.sid;
  ```

- 多表查询

```sql
-- SELECT st.name,sc.score,c.name FROM stu st,score sc,course c WHERE st.id=sc.sid AND sc.cid=c.cid;
SELECT st.name,sc.score,c.name FROM stu st INNER JOIN score sc on st.id=sc.sid INNER JOIN course c on c.cid=sc.cid;
```

- 自然连接：两张连接表的列名称和类型完全一致才会去除相同的列，否则就是笛卡尔积

```sql
SELECT st.name,sc.score FROM student as st NATURAL JOIN score sc where sc.score>60;
```

- 全连接：MySQL不支持全连接，可以使用LEFT JOIN 和UNION和RIGHT JOIN联合使用

```sql
SELECT * FROM A LEFT JOIN B ON A.id=B.id UNIONSELECT  FROM A RIGHT JOIN B ON A.id=B.id
```

### 子查询

- `where`之后，把`select`的查询结果当做另外一个`select`的条件值

```sql
--查询与lisi同一个部门的员工
select deptno from emp where name='lisi';
+--------+
| depton |
+--------+
| 20     |
+--------+
select ename from emp where depton=20;
+--------+-------+
| ename | depton |
+--------+-------+
| zhangsan| 20   |
+--------+-------+
| lisi   | 20    |
+--------+-------+
--合并
select ename from emp where depton=(select deptno from emp where name='lisi');
```

- `from`之后，把查询出的结果当做一个新表

```sql
--查询30号部门的，姓名，薪水
select ename,salary,deptno from emp where deptno=30;
+--------+--------+----------+
| ename | salary  | depton   |
+--------+---------+---------+
| wangwu |  1600  |    30    |
+--------+-----------+-------+
| ydongy |  2200  |    30    |
+--------+-----------+-------+
| ydy    |  2500  |    30    |
+--------+-----------+-------+
--根据上一步的结果作为新表，查询出薪水大于200的员工姓名
select ename from (select ename,salary,deptno from emp where deptno=30) as s where s.salary>2000 ;
```

# 常用函数

## 字符串函数

- `concat`：任何字符串与NULL进行连接结果都是NULL

```sql
select concat('a','b','c');
+---------------------+
| concat('a','b','c') |
+---------------------+
| abc                 |
+---------------------+
1 row in set (0.00 sec)
```

- `insert`：从某个位置开始替换几个字符为新字符

```sql
select insert('mysql',2,1,'a');
+-------------------------+
| insert('mysql',2,1,'a') |
+-------------------------+
| masql                   |
+-------------------------+
1 row in set (0.00 sec)
```

- `lower`和`upper`

```sql
select upper('abC');
+--------------+
| upper('abC') |
+--------------+
| ABC          |
+--------------+
1 row in set (0.00 sec)

mysql> select lower('abC');
+--------------+
| lower('abC') |
+--------------+
| abc          |
+--------------+
1 row in set (0.00 sec)
```

- `left`和`right`：分别返回最左边多少个字符和最右边多少个字符

```sql
mysql> select left('mysql',2);
+-----------------+
| left('mysql',2) |
+-----------------+
| my              |
+-----------------+
1 row in set (0.00 sec)

mysql> select right('mysql',2);
+------------------+
| right('mysql',2) |
+------------------+
| ql               |
+------------------+
1 row in set (0.00 sec)

mysql> select right('mysql',null);
+---------------------+
| right('mysql',null) |
+---------------------+
| NULL                |
+---------------------+
1 row in set (0.00 sec)
```

- `lpad(str,n,pad)`和`rpad(str,n,pad)`：用字符串pad对str左边或右边进行填充，直到字符串长度为n，如果n小于原本字符串长度，会进行截取而不会填充

```sql
mysql> select lpad('mysql',7,'a');
+---------------------+
| lpad('mysql',7,'a') |
+---------------------+
| aamysql             |
+---------------------+
1 row in set (0.00 sec)
```

- `ltrim(str)`和`rtrim(str)`：去掉字符串当中最左侧和最右侧的空格
- `trim(str)`：去除字符串左右两边空格

```sql
mysql> select ltrim('  mysql  ');
+--------------------+
| ltrim('  mysql  ') |
+--------------------+
| mysql              |
+--------------------+
1 row in set (0.00 sec)
```

- `repeat(str,x)`：重复字符串x次的结果

```sql
mysql> select repeat('mysql',3);
+-------------------+
| repeat('mysql',3) |
+-------------------+
| mysqlmysqlmysql   |
+-------------------+
1 row in set (0.00 sec)
```

- `replace(str,a,b)`：把字符串某些字符a替换成字符b

```sql
mysql> select replace('高级Python','Python','MySQL');
+------------------------------------------+
| replace('高级Python','Python','MySQL')   |
+------------------------------------------+
| 高级MySQL                                |
+------------------------------------------+
1 row in set (0.00 sec)
```

- `substring(str,x,y)`：返回字符串str中第x位置开始的y个字符长度的子串

```sql
mysql> select substring('aaabbbccc',3,3);
+----------------------------+
| substring('aaabbbccc',3,3) |
+----------------------------+
| abb                        |
+----------------------------+
1 row in set (0.00 sec)
```

## 数值函数

- `abs(x)`：绝对值
- `ceil(x)`：向上取整

```sql
mysql> select ceil(1.1);
+-----------+
| ceil(1.1) |
+-----------+
|         2 |
+-----------+
1 row in set (0.00 sec)
```

- `floor(x)`：向下取整

```sql
mysql> select floor(1.8);
+------------+
| floor(1.8) |
+------------+
|          1 |
+------------+
1 row in set (0.00 sec)
```

- `mod(x,y)`：就模
- `rand()`：返回0-1的随机值

## 日期时间函数

- `curdate()`：返回当前日期
- `curtime()`：返回当前时间
- `now()`：返回当前日期时间
- `unix_timestamp()`：返回当前时间戳
- `from_unixtime(unixtime)`：将一个时间戳转换成日期
- `week(date)`：返回当前是一年中的第几周
- `year(date)`：返回所给日期是那一年
- `hour(time)`：返回当前时间的小时
- `minute(time)`：返回当前时间的分钟
- `date_format(date,fmt)`：日期格式化

```sql
mysql> select date_format(now(),'%M,%D,%Y');
+-------------------------------+
| date_format(now(),'%M,%D,%Y') |
+-------------------------------+
| July,5th,2020                 |
+-------------------------------+
1 row in set (0.00 sec)
```

- `date_add(date,interval expr type)`：计算时间间隔

```sql
mysql> select date_add(now(),interval 30 day);
+---------------------------------+
| date_add(now(),interval 30 day) |
+---------------------------------+
| 2020-08-04 13:34:53             |
+---------------------------------+
1 row in set (0.00 sec)
```

- `datediff(date1,date2)`：计算两个日期相差多少天

```sql
mysql> select datediff('2020-08-01',now());
+------------------------------+
| datediff('2020-08-01',now()) |
+------------------------------+
|                           27 |
+------------------------------+
1 row in set (0.00 sec)
```

## 流程函数

- `if(value,t,f)`：如果value是真，返回t,否则返回f

```sql
mysql> select if(2>3,'2','3');
+-----------------+
| if(2>3,'2','3') |
+-----------------+
| 3               |
+-----------------+
1 row in set (0.00 sec)
```

- `ifnull(value1,value2)`：如果value1不为空则返回，否则返回value2

- `case when then end`：基本不用

```sql
mysql> select case when 2>3 then '对' else '错' end;
+-----------------------------------------+
| case when 2>3 then '对' else '错' end   |
+-----------------------------------------+
| 错                                      |
+-----------------------------------------+
1 row in set (0.00 sec)
```

## 其它函数

- `database()`：当前数据库
- `version()`：当前版本
- `user()`：当前用户
- `password(str)`：返回加密字符串
- `md5(str)`：返回md5加密串

# 权限操作

### 创建用户

```sql
create user '用户名'@'localhost' identified by '密码'；
```

### 删除用户

```sql
drop user '用户名'@'localhost';
```

### 分配权限

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
FLUSH PRIVILEGES;--刷新权限表
```

- `*.*`：代表所有资源所有权限，即：所有数据库下面的所有表
- `'root'@'%'`：其中`root`代表账户名，`%`代表所有的访问地址，也可以使用一个唯一的地址进行替换，只有一个地址能够访问。如果是某个网段的可以使用地址与`%`结合的方式，如10.0.42.%。
- `IDENTIFIED BY 'root'`：这个`root`是指访问密码。
- `WITH GRANT OPTION`允许级联授权，意思就是该用户还可以给其他用户分配权限。

```sql
--分配指定数据库权限
GRANT ALL PRIVILEGES ON 数据库名.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
FLUSH PRIVILEGES;--刷新权限表
```

```sql
--只对某个表进行CRUD操作
GRANT insert,update,delete,select ON 数据库名.表名 TO 'root'@'%' IDENTIFIED BY 'root';
FLUSH PRIVILEGES;--刷新权限表
```

### 查看权限

```sql
mysql> show grants;
+---------------------------------------------------------------------+
| Grants for root@localhost                                           |
+---------------------------------------------------------------------+
| GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION |
| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION        |
+---------------------------------------------------------------------+
2 rows in set (0.00 sec)
```

```sql
--查看指定用户权限
show grants for root@localhost;
```

### 删除权限

```sql
revoke 权限 on 数据库 from 用户@地址;
```

### 权限列表

```sql
mysql> show privileges;
+-------------------------+---------------------------------------+-------------------------------------------------------+
| Privilege               | Context                               | Comment                                               |
+-------------------------+---------------------------------------+-------------------------------------------------------+
| Alter                   | Tables                                | To alter the table                                    |
| Alter routine           | Functions,Procedures                  | To alter or drop stored functions/procedures          |
| Create                  | Databases,Tables,Indexes              | To create new databases and tables                    |
| Create routine          | Databases                             | To use CREATE FUNCTION/PROCEDURE                      |
| Create temporary tables | Databases                             | To use CREATE TEMPORARY TABLE                         |
| Create view             | Tables                                | To create new views                                   |
| Create user             | Server Admin                          | To create new users                                   |
| Delete                  | Tables                                | To delete existing rows                               |
| Drop                    | Databases,Tables                      | To drop databases, tables, and views                  |
| Event                   | Server Admin                          | To create, alter, drop and execute events             |
| Execute                 | Functions,Procedures                  | To execute stored routines                            |
| File                    | File access on server                 | To read and write files on the server                 |
| Grant option            | Databases,Tables,Functions,Procedures | To give to other users those privileges you possess   |
| Index                   | Tables                                | To create or drop indexes                             |
| Insert                  | Tables                                | To insert data into tables                            |
| Lock tables             | Databases                             | To use LOCK TABLES (together with SELECT privilege)   |
| Process                 | Server Admin                          | To view the plain text of currently executing queries |
| Proxy                   | Server Admin                          | To make proxy user possible                           |
| References              | Databases,Tables                      | To have references on tables                          |
| Reload                  | Server Admin                          | To reload or refresh tables, logs and privileges      |
| Replication client      | Server Admin                          | To ask where the slave or master servers are          |
| Replication slave       | Server Admin                          | To read binary log events from the master             |
| Select                  | Tables                                | To retrieve rows from table                           |
| Show databases          | Server Admin                          | To see all databases with SHOW DATABASES              |
| Show view               | Tables                                | To see views with SHOW CREATE VIEW                    |
| Shutdown                | Server Admin                          | To shut down the server                               |
| Super                   | Server Admin                          | To use KILL thread, SET GLOBAL, CHANGE MASTER, etc.   |
| Trigger                 | Tables                                | To use triggers                                       |
| Create tablespace       | Server Admin                          | To create/alter/drop tablespaces                      |
| Update                  | Tables                                | To update existing rows                               |
| Usage                   | Server Admin                          | No privileges - allow connect only                    |
+-------------------------+---------------------------------------+-------------------------------------------------------+
```

# MySQL

## 连接选项

```shell
参数 ：
    -u, --user=name	    # 指定用户名
    -p, --password[=name]   # 指定密码
    -h, --host=name	    # 指定服务器IP或域名
    -P, --port=		    # 指定连接端口
    -S, --socket=           # 本地socket文件地址
    -e, --execute=name      # 免交互执行数据库命令
    <                       # 导入SQL脚本
```

## 示例

```shell
mysql -h 127.0.0.1 -P 3306 -u root -p

mysql -h127.0.0.1 -P3306 -uroot -p2143

mysql -uroot -proot -e "show privileges;"

# 此选项可以在`Mysql`客户端执行`SQL`语句，而不用连接到`MySQ`L数据库再执行，对于一些批处理脚本，这种方式尤其方便。
```

# Show命令

| 命令                                     | 介绍                           |
| ---------------------------------------- | ------------------------------ |
| `show  databases;`                       | 查看所有数据库                 |
| `show tables;  `                         | 查看当前库的所有表             |
| `SHOW TABLES FROM ;`                     | 查看某个指定库下的表           |
| `show create database db_name ;`         | 查看建库语句                   |
| `show create table db_name.table_name ;` | 查看建表语句                   |
| `show  grants for  root@'localhost'; `   | 查看用户的权限信息             |
| `show  charset；`                        | 查看字符集                     |
| `show collation ; `                      | 查看校对规则                   |
| `show processlist;`                      | 查看数据库连接情况             |
| `show index from table_name;`            | 表的索引情况                   |
| `show status `                           | 数据库状态查看                 |
| `SHOW STATUS LIKE '%lock%';`             | 模糊查询数据库某些状态         |
| `SHOW VARIABLES`                         | 查看所有配置信息               |
| `SHOW variables LIKE '%lock%';`          | 查看部分配置信息               |
| `show engines `                          | 查看支持的所有的存储引擎       |
| `show engine innodb status\G `           | 查看`InnoDB`引擎相关的状态信息 |
| `show binary logs`                       | 列举所有的二进制日志           |
| `show master status`                     | 查看数据库的日志位置信息       |
| `show binlog evnets in `                 | 查看二进制日志事件             |
| `show slave status \G`                   | 查看从库状态                   |
| `SHOW RELAYLOG EVENTS `                  | 查看从库`relaylog`事件信息     |

# 视图

## 创建视图

```sql
-- 查询地区关联的国家
select c.*,t.country_name from city as c ,country as t where c.country_id=t.country_id;

+---------+-----------+------------+--------------+
| city_id | city_name | country_id | country_name |
+---------+-----------+------------+--------------+
|       1 | 西安      |          1 | China        |
|       2 | NewYork   |          2 | America      |
|       3 | 北京      |          1 | China        |
|       4 | 上海      |          1 | China        |
|       5 | 武汉      |          1 | China        |
+---------+-----------+------------+--------------+
5 rows in set (0.00 sec)

-- 创建视图
create view view_city_country as select c.*,t.country_name from city as c ,country as t where c.country_id=t.country_id;
```

## 查询视图数据

```sql
select * from view_city_country;
```

## 更新视图数据，视图建立关联的表也改变了，不建议使用

```sql
update view_city_country set xxx='xxx' where id=xxx;
```

## 修改视图

```sql
alter  view view_city_country as  select * from xxx where xxx=xxx;
```

## 查看视图

```sql
show create view view_city_country;

*************************** 1. row ***************************
                View: view_city_country
         Create View: CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `view_city_country` AS select `c`.`city_id` AS `city_id`,`c`.`city_name` AS `city_name`,`c`.`country_id` AS `country_id`,`t`.`country_name` AS `country_name` from (`city` `c` join `country` `t`) where (`c`.`country_id` = `t`.`country_id`)
character_set_client: utf8
collation_connection: utf8_general_ci
1 row in set (0.01 sec)
```

## 删除视图

```sql
# if exists：表示如果存在就删除

drop view if exists view_city_country;
```

# 索引

```sql
+---------+-----------+------------+
| city_id | city_name | country_id |
+---------+-----------+------------+
|       1 | 西安      |          1 |
|       2 | NewYork   |          2 |
|       3 | 北京      |          1 |
|       4 | 上海      |          1 |
|       5 | 武汉      |          1 |
+---------+-----------+------------+
5 rows in set (0.00 sec)

+------------+--------------+
| country_id | country_name |
+------------+--------------+
|          1 | China        |
|          2 | America      |
|          3 | Japan        |
|          4 | UK           |
+------------+--------------+
4 rows in set (0.00 sec)
```

## 创建索引

```sql
create [UNIQUE|FULLTEXT|SPATIAL] index index_name [USING index_type]
on tbl_name(index_col_name)

index_name:索引名称
tbl_name：表名
index_col_name：字段名

# 例如：为学生表的姓名字段创建索引
create index idx_city_name on city(city_name);
```

## 创建复合索引

```sql
create index index_name on tb_name(字段1,字段2,字段3...);
```

## 查看索引

```sql
show index from students;
show index from students\G;

+-------+------------+---------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name      | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+---------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| city  |          0 | PRIMARY       |            1 | city_id     | A         |           5 |     NULL | NULL   |      | BTREE      |         |               |
| city  |          1 | idx_city_name |            1 | city_name   | A         |           5 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+---------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)
```

## 删除索引

```sql
drop index idx_city_name  on city;
```
## ALTER添加索引

```sql
# 添加主键索引，值唯一，不能为NULL
alter table tb_name add primary key(column_list);

# 创建值唯一索引，可以为NULL且可以存在多个NULL
alter table tb_name add unique index_name(column_list);

# 普通索引
alter table tb_name add index index_name(column_list);

# 全文索引
alter table tb_name add fulltext index_name(column_list);
```

# 存储过程和函数

> 函数：有返回值的过程
> 过程：没有返回值的函数
{.is-info}

## 修改 mysql 默认分号结尾执行语句

```sql
delimiter $
```

## 创建存储过程

```sql
# 此处的$是更改了默认分号结尾
create procedure procedure_name()
begin
 -- SQL语句
end$

# 例如：
mysql> create procedure test()
    -> begin
    -> select 'hello';
    -> end$
```

## 调用存储过程

```sql
# 此处的$是更改了默认分号结尾
call test()$

+-------+
| hello |
+-------+
| hello |
+-------+
1 row in set (0.02 sec)
```

## 查看存储过程

```sql
# 查询db_name数据库中的所有存储过程
# 此处的$是更改了默认分号结尾
select name from mysql.proc where db = 'db_name'$

+------+
| name |
+------+
| test |
+------+
1 row in set (0.02 sec)

show procedure  status\G$

-- 查看存储过程的定义，即当时创建使用的语法
show create procedure test\G$

*************************** 1. row ***************************
           Procedure: test
            sql_mode: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
    Create Procedure: CREATE DEFINER=`root`@`localhost` PROCEDURE `test`()
begin
select 'hello';
end
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.00 sec)
```

## 删除存储过程

```sql
-- 此处的 $ 是更改了默认分号结尾
drop procedure test$
```

# 存储过程语法

> 存储过程是可以编程的，意味这可以使用变量，表达式，控制结构，来完成比较复杂的功能。
{.is-info}


## 变量

> `DECLARE variable_name [,variable_name...] datatype [DEFAULT value];`其中，datatype 为 MySQL 的数据类型，如: int, float, date,varchar(length)
{.is-info}


- `declare`：定义变量

```sql
mysql> create procedure pro_test()
    -> begin
    -> declare num int default 10;
    -> select concat('num的值为:',num);　#　打印
    -> end$

-- 调用
call pro_test()$

+-----------------------------+
| concat('num的值为:',num)     |
+-----------------------------+
| num的值为:10                 |
+-----------------------------+
1 row in set (0.00 sec)
```

- `set`：设置变量

```sql
-- 方式一
mysql> create procedure pro_test2()
    -> begin
    -> declare num int default 10;
    -> set num = num + 10;
    -> select num;
    -> end$

call pro_test2()$

+------+
| num  |
+------+
|   20 |
+------+
1 row in set (0.00 sec)

-- 方式二
mysql> create procedure pro_test3()
    -> begin
    -> declare num int;
    -> select count(*) into num from city;
    -> select concat('city表中的记录数:',num);
    -> end$

call pro_test3()$

+---------------------------------------+
| concat('city表中的记录数:',num)         |
+---------------------------------------+
| city表中的记录数:5                      |
+---------------------------------------+
1 row in set (0.00 sec)
```

## if 条件判断

> 根据定义的身高变量，判断当前身高所属的身材。
{.is-info}


- 180及以上－－－－>高
- 170-180－－－－>标准
- 170以下－－－－>矮

```sql
create procedure pro_test4()
begin
    declare height int default 175;
    declare description varchar(50) default '';
    if height>=180 then
        set description='高';
    elseif height>=170 and height<180 then
        set description='标准';
    else
        set description='矮';
    end if;
    select concat('身高：',height,'身材：',description);
end$

call pro_test4()$

+----------------------------------------------------+
| concat('身高：',height,'身材：',description)       |
+----------------------------------------------------+
| 身高：175身材：标准                                |
+----------------------------------------------------+
1 row in set (0.00 sec)
```

## 传递参数

- in：输入参数
- out：输出参数，相当于返回值
- inout：既可以是输入参数，又可以是输出参数


```sql
create procedure pro_test5(in height int)
begin
    declare description varchar(50) default '';
    if height>=180 then
        set description='高';
    elseif height>=170 and height<180 then
        set description='标准';
    else
        set description='矮';
    end if;
    select concat('身高：',height,'身材：',description);
end$

call pro_test5(198)$

+----------------------------------------------------+
| concat('身高：',height,'身材：',description)       |
+----------------------------------------------------+
| 身高：198身材：高                                  |
+----------------------------------------------------+
1 row in set (0.00 sec)
```

```sql
-- 传入参数height，返回description

create procedure pro_test6(in height int,out description varchar(10))
begin
    if height>=180 then
        set description='高';
    elseif height>=170 and height<180 then
        set description='标准';
    else
        set description='矮';
    end if;
end;

call pro_test6(188,@description)$ -- 值返回到了`@description`，@表示用户会话变量，仅用于当前会话

select @description$

+--------------+
| @description |
+--------------+
| 高           |
+--------------+
1 row in set (0.00 sec)
```

## case 结构

给定一个月份，计算所在的季度

```sql
create procedure pro_test7(in mon int)
begin
    declare result varchar(10);
    case
        when mon >= 1 and mon <= 3 then
            set result = '第一季度';
        when mon >= 4 and mon <= 6 then
            set result = '第二季度';
        when mon >= 7 and mon <= 9 then
            set result = '第三季度';
        else
            set result = '第四季度';
        end case;
    select concat('月份：', mon, '季度：', result);
end$

call pro_test7(1)$

+--------------------------------------------+
| concat('月份：',mon,'季度：',result)       |
+--------------------------------------------+
| 月份：1季度：第一季度                      |
+--------------------------------------------+
1 row in set (0.00 sec)
```

## while 循环

> 计算从 1 加到 n 的值
{.is-info}


```sql
create procedure pro_test8(in n int)
begin
    declare sum int default 0;
    declare num int default 1;
    while num <= n
        do
            set sum = sum + num;
            set num = num + 1;
        end while;
    select sum;
end$

call pro_test8(4)$

+------+
| sum  |
+------+
|   10 |
+------+
1 row in set (0.00 sec)
```

## repeat 循环

> 满足条件退出循环
{.is-info}


```sql
create procedure pro_test9(in n int)
begin
    declare sum int default 0;
    repeat
        set sum = sum + n;
        set n = n - 1;
    until n = 0 end repeat; #　until满足退出循环
    select sum;
end$

call pro_test9(3)$

+------+
| sum  |
+------+
|    6 |
+------+
1 row in set (0.00 sec)
```

## loop 循环

> leave 语句退出循环
{.is-info}


```sql
create procedure pro_test10(in n int)
begin
    declare sum int default 0;
    # c　是循环的别名
    c:loop
        set sum = sum + n;
        set n = n - 1;

        if n <=0 then
            leave c;
        end if;
    end loop c;
    select sum;
end;

call pro_test10(10)$

+------+
| sum  |
+------+
|   55 |
+------+
1 row in set (0.00 sec)
```

## 游标

> 用来存储查询结果集的，便于遍历结果集
{.is-info}



```sql
create procedure pro_test11()
begin
    declare c_id int(11);
    declare c_name varchar(50);
    declare c_country_id int(11);
    declare city_result cursor for select * from city;

    open city_result;
    #　fetch 几次打印几次，当次数操作查询总数，最后报错
    fetch city_result into c_id,c_name,c_country_id;
    select concat('id=', c_id, 'name=', c_name, 'country_id', c_country_id);

    fetch city_result into c_id,c_name,c_country_id;
    select concat('id=', c_id, 'name=', c_name, 'country_id', c_country_id);

    close city_result;
end;

call pro_test11()$

+------------------------------------------------------------------+
| concat('id=', c_id, 'name=', c_name, 'country_id', c_country_id) |
+------------------------------------------------------------------+
| id=1name=西安country_id1                                         |
+------------------------------------------------------------------+
1 row in set (0.00 sec)

+------------------------------------------------------------------+
| concat('id=', c_id, 'name=', c_name, 'country_id', c_country_id) |
+------------------------------------------------------------------+
| id=2name=NewYorkcountry_id2                                      |
+------------------------------------------------------------------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
```

- **循环版本**

```sql
create procedure pro_test12()
begin
    declare c_id int(11);
    declare c_name varchar(50);
    declare c_country_id int(11);

    declare has_data int default 1;

    declare city_result cursor for select * from city;
    #　当抓取不到数据,把has_data设置为0,循环退出
    #　此句必须卸载游标声明之后
    declare exit handler for not found set has_data = 0;

    open city_result;

    repeat
        fetch city_result into c_id,c_name,c_country_id;
        select concat('id=', c_id, 'name=', c_name, 'country_id', c_country_id);
    until has_data = 0 end repeat;

    close city_result;
end;
```

# 存储函数

定义一个存储函数，返回满足查询条件的的总记录数

```sql
create function func_test(countryId int)
    returns int
begin
    declare cnum int;
    select count(*) into cnum from city where country_id = countryId;
    return cnum;
end;

-- 调用
select func_test(2)$

+--------------+
| func_test(2) |
+--------------+
|            1 |
+--------------+
1 row in set (0.00 sec)
```

# 触发器

>触发器是与表有关的数据库对象，指在insert/update/delete之前或之后触发并执行触发器中定义的SQL语句。触发器的这种特性由于应用在数据库端确保数据的完整性，日志记录，数据校验等操作。

- **创建日志表**

```sql
create table city_logs
(
    id             int(11)     not null auto_increment,
    operation      varchar(20) not null comment '操作类型，insert/update/delete',
    operate_time   datetime    not null comment '操作时间',
    operate_id     int(11)     not null comment '操作表id',
    operate_params varchar(500) comment '操作参数',
    primary key (id)
) engine = innodb
  default charset = utf8$
```

## 创建触发器

通过触发器记录city表的变化日志

```sql
create trigger city_insert_trigger
    #　插入之后触发
    after insert
    #　触发关联的表
    on city
    #　行级
    for each row
begin
    insert into city_logs(id, operation, operate_time, operate_id, operate_params)
    values (null, 'insert', now(), NEW.city_id,
            concat('插入后数据为:(id:', NEW.city_id, ',name:', NEW.city_name, ',country_id:', NEW.country_id, ')'));
end;

insert into `city` (`city_id`, `city_name`, `country_id`) value (6, '深圳', 1)$

-- 插入完成之后查询city_logs
select * from city_logs$

+----+-----------+---------------------+------------+----------------------------------------------------+
| id | operation | operate_time        | operate_id | operate_params                                     |
+----+-----------+---------------------+------------+----------------------------------------------------+
|  1 | insert    | 2020-06-10 16:37:17 |          6 | 插入后数据为:(id:6,name:深圳,country_id:1)         |
+----+-----------+---------------------+------------+----------------------------------------------------+
1 row in set (0.00 sec)
```

## 查看触发器

```sql
show triggers\G$

*************************** 1. row ***************************
             Trigger: city_insert_trigger
               Event: INSERT
               Table: city
           Statement: begin
    insert into city_logs(id, operation, operate_time, operate_id, operate_params)
    values (null, 'insert', now(), NEW.city_id,
            concat('插入后数据为:(id:', NEW.city_id, ',name:', NEW.city_name, ',country_id:', NEW.country_id, ')'));
end
              Timing: AFTER
             Created: 2020-06-10 16:35:53.32
            sql_mode: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
             Definer: root@localhost
character_set_client: utf8
collation_connection: utf8_general_ci
  Database Collation: utf8_general_ci
1 row in set (0.00 sec)
```

## 删除触发器

```sql
drop trigger xxxx;
```

# 事务

```sql
# 开启事务
begin/start transaction

# 提交事务
commit

# 回滚事务
rollback
```

# 与Python交互

```python
from pymysql import *
def main():
    # 创建Connection链接
    conn = connect(host="localhost", port=3306,
                   database="jing_dong", user="root", password="root", charset="utf8")

    # 获得Cursor对象
    cs = conn.cursor()

    count = cs.execute('select * from goods')
    # 打印受影响的行数
    print(count)
    print(cs.fetchall())

    # 增加
    # count2 = cs.execute('insert into goods_cates(name) values ("硬盘")')

    # 更新
    # cs.execute('update goods_cates set name="机械硬盘" where name="硬盘"')

    # 删除
    # cs.execute('delete from goods_cates where name="机械硬盘"')
    # 提交之前的操作，如果之前已经之执行过多次的execute，那么就都进行提交
    conn.commit()

    # 关闭Cursor对象
    cs.close()
    # 关闭Connection对象
    conn.close()


if __name__ == '__main__':
    main()
```




