---
title: DateTimeField
description: 
published: true
date: 2021-03-21T13:35:50.974Z
tags: 
editor: markdown
dateCreated: 2021-03-21T13:35:50.974Z
---

# DateTimeField

DateTimeField 是存储日期的日期和时间字段，在 Python 中由 `datetime.datetime` 实例表示。顾名思义，该字段用于存储在 python 中创建的 datetime 对象。此字段的默认表单窗口小部件是 TextInput

```python
field_name = models.DateTimeField（** options）
```

DateTimeField 具有以下额外的可选参数：

- `auto_now`：与 DateField 相同
- `auto_now_add`：与 DateField 相同

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(Model): 
    geeks_field = models.DateTimeField() 
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
                ('geeks_field', models.DateTimeField()),
            ],
        ),
    ]
```

## 如何使用 DateTimeField？

```python
from geeks.models import GeeksModel 

import datetime 

# 创建一个 datetime.datetime 对象
d = datetime.datetime(2015, 10, 09, 23, 55, 59, 342380) 

# 创建一个模型对象并保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![datetimefield.png](/assets/web框架/django/模型字段/datetimefield.png)

