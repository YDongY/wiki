---
title: 执行原生 SQL 查询
description: 
published: true
date: 2021-03-19T16:05:18.290Z
tags: 
editor: markdown
dateCreated: 2021-03-19T16:04:49.849Z
---


> [官方文档介绍](https://docs.djangoproject.com/zh-hans/3.1/topics/db/sql/#performing-raw-sql-queries)
{.is-success}


Django 允许你用两种方式执行原生 SQL 查询：

1. 使用 `Manager.raw()` 来执行原生查询并返回模型实例，
2. 完全不用模型层直接执行自定义 SQL。

# 执行原生查询

`Manager.raw(raw_query, params=None, translations=None)`：

该方法接受一个原生 SQL 查询语句，执行它，并返回一个 `django.db.models.query.RawQuerySet` 实例。这个 RawQuerySet 能像普通的 QuerySet 一样被迭代获取对象实例

例如：

```python
class Person(models.Model):
    first_name = models.CharField(...)
    last_name = models.CharField(...)
    birth_date = models.DateField(...)
```

```shell
>>> for p in Person.objects.raw('SELECT * FROM myapp_person'):
...     print(p)
John Smith
Jane Jones
```

> 注意：① Django 不会对传给 .raw() 的 SQL 语句做任何检查 ② 若你在 MySQL 上执行查询，可能会因为强制类型导致不可预料的后果。若你用一个整数值查询一个字符串列，MySQL 会执行比较前将表中所有数据强制转为整数
{.is-warning}

# 将查询字段映射为模型字段

- 指定模型字段名

```shell
>>> Person.objects.raw('SELECT id, first_name, last_name, birth_date FROM myapp_person')
```

- 使用 SQL 的 AS 子句将查询语句中的字段映射至模型中的字段

```shell
>>> Person.objects.raw('''SELECT first AS first_name,
...                              last AS last_name,
...                              bd AS birth_date,
...                              pk AS id,
...                       FROM some_other_table''')
```

- 用 raw() 的 translations 参数将查询语句中的字段映射至模型中的字段

```shell
>>> name_map = {'first': 'first_name', 'last': 'last_name', 'bd': 'birth_date', 'pk': 'id'}
>>> Person.objects.raw('SELECT * FROM some_other_table', translations=name_map)
```

# 索引查询

- 获取第一个结果

```shell
>>> first_person = Person.objects.raw('SELECT * FROM myapp_person')[0]
```

不过，索引和切片不是在数据库层面上实现的。若数据库中有非常多的 Person 对象，更搞笑的方式是在 SQL 层面使用 limit 子句:

```python
>>> first_person = Person.objects.raw('SELECT * FROM myapp_person LIMIT 1')[0]
```

# 延迟模型字段

- 查询返回的 Person 对象即延迟模型实例，这意味着查询语句中省略的字段按需加载

```shell
>>> people = Person.objects.raw('SELECT id, first_name FROM myapp_person')
```

```shell
>>> for p in Person.objects.raw('SELECT id, first_name FROM myapp_person'):
...     print(p.first_name, # 原始查询
...           p.last_name) # 按需加载
...
John Smith
Jane Jones
```

表面上，看起来该查询同时检出了 first name 和 last name。然而，这个例子实际上执行了三次查询。只有 first names 是由 raw() 查询检出的 —— last names 是在它们被打印时按需检出。


# 添加注释

可以执行带有模型中未定义字段的查询语句

```shell
>>> people = Person.objects.raw('SELECT *, age(birth_date) AS age FROM myapp_person')
>>> for p in people:
...     print("%s is %s." % (p.first_name, p.age))
John is 37.
Jane is 42.
...
```

> 可以用 Func() 表达式 避免使用原生 SQL 去计算注释。
{.is-info}


# 将参数传给 raw()

如果需要执行参数化的查询，可以使用 raw() 的 params 参数:

```shell
>>> lname = 'Doe'
>>> Person.objects.raw('SELECT * FROM myapp_person WHERE last_name = %s', [lname])
```


> 不要对原生查询或 SQL 字符串中的引号占位符使用字符串格式化！
>```shell
>>>> query = 'SELECT * FROM myapp_person WHERE last_name = %s' % >lname
>>>> Person.objects.raw(query)
>```
>使用 params 参数和不用引号包裹占位符使你免受 SQL 注入攻击，
>```shell
>>>> query = "SELECT * FROM myapp_person WHERE last_name = '%s'"
>```


# 直接执行自定义 SQL

对象 `django.db.connection` 代表默认数据库连接。要使用这个数据库连接，调用 `connection.cursor()` 来获取一个指针对象。然后，调用 `cursor.execute(sql, [params])` 来执行该 SQL 和 `cursor.fetchone()`，或 `cursor.fetchall()` 获取结果数据。

例如:

```python
from django.db import connection

def my_custom_sql(self):
    with connection.cursor() as cursor:
        cursor.execute("UPDATE bar SET foo = 1 WHERE baz = %s", [self.baz])
        cursor.execute("SELECT foo FROM bar WHERE baz = %s", [self.baz])
        row = cursor.fetchone()

    return row
```

> 要避免 SQL 注入，你绝对不能在 SQL 字符串中用引号包裹 %s 占位符。
{.is-warning}

注意，若要在查询中包含文本的百分号，你需要在传入参数使用两个百分号:


```python
cursor.execute("SELECT foo FROM bar WHERE baz = '30%'")
cursor.execute("SELECT foo FROM bar WHERE baz = '30%%' AND id = %s", [self.id])
```

若你同时使用不止一个数据库，你可以使用 `django.db.connections` 获取指定数据库的连接（和指针）。 `django.db.connections` 是一个类字典对象，它允许你通过连接别名获取指定连接:

```python
from django.db import connections
with connections['my_db_alias'].cursor() as cursor:
				pass
```

默认情况下，Python DB API 返回的结果不会包含字段名，这意味着你最终会收到一个 list，而不是一个 dict。要追求较少的运算和内存消耗，你可以以 dict 返回结果，通过使用如下的玩意:

```python
def dictfetchall(cursor):
    "Return all rows from a cursor as a dict"
    columns = [col[0] for col in cursor.description]
    return [
        dict(zip(columns, row))
        for row in cursor.fetchall()
    ]
```

另一个选项是使用来自 Python 标准库的 collections.namedtuple()。 

```python
from collections import namedtuple

def namedtuplefetchall(cursor):
    "Return all rows from a cursor as a namedtuple"
    desc = cursor.description
    nt_result = namedtuple('Result', [col[0] for col in desc])
    return [nt_result(*row) for row in cursor.fetchall()]
```


```python
>>> cursor.execute("SELECT id, parent_id FROM test LIMIT 2");
>>> cursor.fetchall()
((54360982, None), (54360880, None))

>>> cursor.execute("SELECT id, parent_id FROM test LIMIT 2");
>>> dictfetchall(cursor)
[{'parent_id': None, 'id': 54360982}, {'parent_id': None, 'id': 54360880}]

>>> cursor.execute("SELECT id, parent_id FROM test LIMIT 2");
>>> results = namedtuplefetchall(cursor)
>>> results
[Result(id=54360982, parent_id=None), Result(id=54360880, parent_id=None)]
>>> results[0].id
54360982
>>> results[0][0]
54360982
```
