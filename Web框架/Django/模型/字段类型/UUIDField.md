---
title: UUIDField
description: 
published: true
date: 2021-03-21T13:03:59.402Z
tags: 
editor: markdown
dateCreated: 2021-03-21T12:29:06.300Z
---

# UUIDField

UUIDField 是一个特殊字段，用于存储通用的唯一标识符。它使用 Python 的 UUID 类，生成 128 位的随机 ID 对象。

```python
field_name = models.UUIDField（** options）
```

通用的唯一标识符可以作为 AutoField 主键。由于数据库不会自动生成 UUID，因此建议使用 default。

```python
import uuid 
from django.db import models 

class MyUUIDModel(models.Model): 
	id = models.UUIDField( primary_key = True, default = uuid.uuid4, editable = False) 
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.UUIDField()
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
                ('geeks_field', models.UUIDField()),
            ],
        ),
    ]
```

## 如何使用 UUIDField？

```python
from geeks.models import GeeksModel 

import uuid 

# 创建一个 uuid object 
test = uuid.uuid4() 

# 创建一个模型对象并保存
geek_object = GeeksModel.objects.create(geeks_field = test) 
geek_object.save() 
```

![uuidfield.png](/assets/web框架/django/模型字段/uuidfield.png)

