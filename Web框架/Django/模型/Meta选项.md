---
title: Meta 选项
description: 
published: true
date: 2021-03-19T14:02:12.030Z
tags: 
editor: markdown
dateCreated: 2021-03-19T09:01:36.999Z
---

# abstract

在 Meta 类中填入 `abstract=True`。该模型将不会创建任何数据表。当其用作其它模型类的基类时，它的字段会自动添加至子类。

```python
from django.db import models

class CommonInfo(models.Model):
    name = models.CharField(max_length=100)
    age = models.PositiveIntegerField()

    class Meta:
        abstract = True

class Student(CommonInfo):
    home_group = models.CharField(max_length=5)
```

# app_label

如果定义模型的应用不在 INSTALLED_APPS 中，必须指定所属的应用

# base_manager_name

# db_table

默认情况下一个模型的数据库表名为 `应用名_小写模型名`。通过 `db_table` 可以指定用于模型的数据库表的名称，强烈建议使用小写的表名

```python
class Student(modes.Model):
    # ...
    class Meta:
        db_table = 'student_info'
```

# db_tablespace

指定模型要使用的**数据库表空间**名称。默认是项目的 `DEFAULT_TABLESPACE` 配置。如果后端不支持表空间，则忽略此选项。

# default_manager_name

模型的 `_default_manager` 管理器名称

# default_related_name

关联的对象回指这个模型默认使用的名称。默认为 `<model_name>_set`。

# get_latest_by

该属性的值是字段名或字段名列表，通常是 DateField，DateTimeField 或 IntegerField。使用该参数可以修改默认`last()` 和 `earliest()` 方法的过滤规则。

例如：

```python
# Latest by ascending order_date.
get_latest_by = "order_date"

# Latest by priority descending, order_date ascending.
get_latest_by = ['-priority', 'order_date']
```

# managed

默认为 True，即让 Django 在迁移中创建适当的数据库表，并在执行 flush 管理命令时把表删除。

# order_with_respect_to

标记对象为可排序的，排序依据是指定的字段。

# ordering

用于获取对象列表时，对象的默认排序：

```python
ordering = ['-order_date'] # 降序
ordering = ['order_date'] # 升序
ordering = '?' # 随机排序
ordering = ['-pub_date', 'author'] # 按 pub_date 降序，然后按 author 升序
```

# permissions

创建此对象时，要在权限表中输入额外的权限。 将为每个模型自动创建添加，更改，删除和查看权限。

```python
    class Meta:
        permissions = [
            ("export", "Can export candidate list"),
            ("notify", "notify interviewer for candidate review"),
        ]
```

# default_permissions

默认值为 `('add', 'change', 'delete', 'view')` 。你可以自定义这个列表，例如，如果你的应用不需要任何默认的权限，可以将其设置为空列表。它必须在模型创建之前通过 migrate 在模型上指定，以防止任何遗漏的权限被创建。


# proxy

如果 `proxy = True`，作为另一个模型子类的模型将被视为代理模型。


# indexes

# unique_together

创建联合唯一

```python
unique_together = ['driver', 'restaurant']
```

# index_together

创建联合唯一索引

```python
index_together = ["pub_date", "deadline"]
```

# constraints

创建约束

```python
from django.db import models

class Customer(models.Model):
    age = models.IntegerField()

    class Meta:
        constraints = [
            models.CheckConstraint(check=models.Q(age__gte=18), name='age_gte_18'),
        ]
```

# verbose_name

对象的可读名称，单数：

```python
verbose_name = "pizza"
```

如果没有给定，Django 将使用一个 munged 版本的类名：CamelCase 变成 camel case。


# verbose_name_plural

对象的复数名称：

```python
verbose_name_plural = "stories"
```
如果没有给定，Django 将使用 `verbose_name + "s"`。

# 只读的 Meta 属性

## label

对象的表示，返回 `app_label.object_name`，例如 `'polls.Question'`。

## label_lower

模型的表示，返回 `app_label.model_name`，例如 `'polls.question'`。











