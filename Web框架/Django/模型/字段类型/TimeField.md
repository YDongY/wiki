---
title: TimeField
description: 
published: true
date: 2021-03-21T13:02:19.023Z
tags: 
editor: markdown
dateCreated: 2021-03-21T12:16:01.954Z
---

# TimeField

TimeField 是一个存储时间的时间字段，在 Python 中用 datetime.time 实例表示。顾名思义，该字段用于存储在 python 中创建的 datetime 对象。此字段的默认表单窗口小部件是 TextInput

```python
field_name = models.TimeField（auto_now = False，auto_now_add = False，** options）
```

TimeField具有以下额外的可选参数：

- `auto_now`：与 DateField 相同
- `auto_now_add`：与 DateField 相同

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel15(models.Model):
    geeks_field = models.TimeField()
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
                ('geeks_field', models.TimeField()),
            ],
        ),
    ]
```

## 如何使用 TimeField？

```python
from geeks.models import GeeksModel 
import datetime 

# 创建一个 datetime.time 对象
# time(hour = 0, minute = 0, second = 0) 
d = datetime.time(10, 33, 45) 

# 创建模型对象并保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![timefield.png](/assets/web框架/django/模型字段/timefield.png)
