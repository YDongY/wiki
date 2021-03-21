---
title: DurationField
description: 
published: true
date: 2021-03-21T12:56:58.160Z
tags: 
editor: markdown
dateCreated: 2021-03-21T10:47:54.142Z
---

# DurationField

DurationField 在 MySQL 数据库上存储采用 bigint 类型，存储持续时间，即两个日期或时间之间的差。

```python
field_name = models.DurationField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.DurationField()
```

然后再配置文件中注册应用，并执行迁移命令，生成成迁移文件如下：

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
                ('geeks_field', models.DurationField()),
            ],
        ),
    ]
```

## 如何使用 DurationField？

```python
from geeks.models import GeeksModel 
import datetime 
  
# 创建一个 datetime.timedelta 实例 
d = datetime.timedelta(days =-1, seconds = 68400) 
  
# 创建一个模型对象并保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![durationfield.png](/assets/web框架/django/模型字段/durationfield.png)

