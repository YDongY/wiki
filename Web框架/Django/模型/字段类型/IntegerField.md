---
title: IntegerField
description: 
published: true
date: 2021-03-21T11:22:10.361Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:21:46.950Z
---

# IntegerField 

IntegerField 是 Python 中由 int 实例表示的整数。此字段通常用于在数据库中存储整数。支持-2147483648 到 2147483647 之间的值。使用 MinValueValidator 和 MaxValueValidator 根据默认数据库支持的值来验证输入。

```python
field_name = models.IntegerField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(Model): 
    geeks_field = models.IntegerField() 
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
                ('geeks_field', models.IntegerField()),
            ],
        ),
    ]
```

## 如何使用 IntegerField？

```python
from geeks.models import GeeksModel 

# 创建一个 int 对象
d = int(2189) 

# 创建一个模型对象并保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![integerfield.png](/assets/web框架/django/模型字段/integerfield.png)
