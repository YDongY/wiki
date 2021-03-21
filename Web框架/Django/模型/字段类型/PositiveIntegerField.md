---
title: PositiveIntegerField
description: 
published: true
date: 2021-03-21T13:00:26.856Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:35:53.953Z
---

# PositiveIntegerField

PositiveIntegerField 在 Python 中是由 int 实例表示的整数。该字段类似于 IntegerField，但必须为正数或零（0），支持从 0 到 2147483647 的值

```python
field_name = models.PositiveIntegerField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel10(models.Model):
    geeks_field = models.PositiveIntegerField()
```

然后再配置文件中注册应用，并执行迁移命令，生成成迁移文件如下：

```python
from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='GeeksModel10',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('geeks_field', models.PositiveIntegerField()),
            ],
        ),
    ]
```

![positiveintegerfield.png](/assets/web框架/django/模型字段/positiveintegerfield.png)

