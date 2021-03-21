---
title: PositiveSmallIntegerField
description: 
published: true
date: 2021-03-21T13:00:56.747Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:52:27.620Z
---

# PositiveSmallIntegerField

PositiveSmallIntegerField 是在 Python 中由 int 实例表示的整数。这个字段就像一个 PositiveIntegerField。它在 Django 支持的所有数据库中支持 0 到 32767 的值

```python
field_name = models.PositiveSmallIntegerField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.PositiveSmallIntegerField()
```

然后再配置文件中注册应用，并执行迁移命令，生成迁移文件如下：

```python
from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='GeeksModel',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('geeks_field', models.PositiveSmallIntegerField()),
            ],
        ),
    ]
```
