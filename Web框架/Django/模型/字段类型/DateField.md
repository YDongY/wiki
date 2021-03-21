---
title: DateField
description: 
published: true
date: 2021-03-21T12:55:57.516Z
tags: 
editor: markdown
dateCreated: 2021-03-21T10:26:30.604Z
---

# DateField

DateField 是一个存储日期的字段，该字段用于存储在 python 中创建的 datetime.date 对象。此字段的默认表单窗口小部件是 TextInput

```python
field_name = models.DateField（** options）
```

DateField 具有以下额外的可选参数：

- `auto_now`：每次调用 `Model.save()` 时自动将字段设置为现在。一般用来保存最后修改时间
- `auto_now_add`：首次创建对象时，将字段自动设置为现在时间，一般用来保存当前时间

如果希望能够修改此字段设置以下内容，而不要设置 `auto_now_add = True`：

- 对于 `DateField` 可以设置为 `default=date.today` 会调用 `datetime.date.today()`
- 对于 `DateTimeField`: `default=timezone.now` 会调用 `django.utils.timezone.now()`

> auto_now_add，auto_now 和 default 是互斥的
{.is-warning}

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.DateField()
```

然后再配置文件中注册应用，并执行迁移命令，生成成迁移文件如下：

```python
from django.db import migrations, models


class Migration(migrations.Migration):

    initial = True

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='GeeksModel',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('geeks_field', models.DateField()),
            ],
        ),
    ]
```

## 如何使用 DateField？

```python
from geeks.models import GeeksModel 
import datetime 
  
# 创建一个 datetime.date 对象
d = datetime.date(1997, 10, 19) 
  
# 创建一个模型实例保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![datefield.png](/assets/web框架/django/模型字段/datefield.png)
