---
title: F 表达式
description: 
published: true
date: 2021-02-25T11:47:24.483Z
tags: django
editor: markdown
dateCreated: 2021-02-25T10:22:16.109Z
---

> [官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/#f-expressions)
{.is-success}

# F() 表达式

有时需要在一个字段上执行一个简单的算术任务，比如递增或递减当前值。一种方法是在 Python 中进行运算，比如：

```python
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save()
```

这一过程是从数据库中提取了 reporter.stories_filed 的值到内存中，使用 Python 操作符对其进行操作，然后将对象保存回数据库

然而使用 F() 对象操作方法如下：

```python
from django.db.models import F

reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save()
```

看似是一个普通的 Python 赋值给一个实例属性 ，实际当 Django 遇到 F() 的实例时，它会覆盖标准的 Python 运算符来创建一个封装的 SQL 表达式，在数据库层面描述所需的操作。Python 层面并不知道 reporter.stories_filed 的值是多少。

要访问这样保存的新值，必须重新加载对象：

```python
reporter = Reporters.objects.get(pk=reporter.pk)
# Or, more succinctly:
reporter.refresh_from_db()
```

F() 还可以与 update() 一起用于对象实例的 QuerySets，从而把上面使用的两个查询 get() 和 save() 减少到只有一个：

```python
reporter = Reporters.objects.filter(name='Tintin')
reporter.update(stories_filed=F('stories_filed') + 1)
```

如果想要递增多个对象上的字段值，可以使用下面方式，这比遍历递增每个对象的字段值，然后把每个对象保存回数据库要快得多：

```python
Reporter.objects.all().update(stories_filed=F('stories_filed') + 1)
```

因此，F() 可以通过以下方式提供性能优势：

- **让数据库，而不是 Python 来完成工作**
- **减少某些操作所需的查询次数**

## 使用 F() 避免竞争条件。

如果两个 Python 线程执行下面的代码，一个线程可以在另一个线程从数据库中获取一个字段的值后，检索、递增并保存它。第二个线程保存的值将基于原始值，从而导致第一个线程的工作并不是基于第二个线程只有的结果。

```python
reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed += 1
reporter.save()
```

如果数据库负责更新字段，那么这个过程就比较稳健：它只会在执行 `save()` 或 `update()` 时，根据数据库中字段的值来更新字段，而不是根据检索实例时的值来更新。

## F() 赋值在 Model.save() 之后持续存在

例如：

```reporter = Reporters.objects.get(name='Tintin')
reporter.stories_filed = F('stories_filed') + 1
reporter.save()

reporter.name = 'Tintin Jr.'
reporter.save()python
```

在这种情况下，stories_filed 将被更新两次。如果最初是 1，最终值将是 3。这种持久性可以通过在保存模型对象后重新加载来避免，例如，使用 refresh_from_db()。

## 在过滤器中使用 F()

Django 中 F() 的实例充当查询中的模型字段的引用。这些引用可在查询过滤器中用于在同一模型实例中比较两个不同的字段。

例：查询图书阅读量大于评论量图书信息。

```python
from django.db.models import F
BookInfo.objects.filter(bread__gt=F('bcomment'))
```

例：查询图书阅读量大于2倍评论量图书信息。

```python
BookInfo.objects.filter(bread__gt=F('bcomment')*2)
```

例：查询图书阅读量大于评论量+点赞量图书信息。

```python
BookInfo.objects.filter(bread__gt=F('bcomment')+F('blike'))
```

对于 date 和 datetime 字段，可以加上或减去一个 timedelta 对象。以下会返回所有创建 3 天后被修改的条目:

```python
from datetime import timedelta
BookInfo.objects.filter(modifidy_at__gt=F('created_at') + timedelta(days=3))
```

F() 对象通过 `.bitand()`， `.bitor()`， `.bitxor()`，`.bitrightshift()` 和 `.bitleftshift()` 支持位操作。例子:

```python
F('somefield').bitand(16)
```

> 更多用法，请参考[官方文档](https://docs.djangoproject.com/zh-hans/3.1/ref/models/expressions/#using-f-with-annotations)
{.is-info}

