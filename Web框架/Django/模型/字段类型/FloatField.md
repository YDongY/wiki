---
title: FloatField
description: 
published: true
date: 2021-03-21T11:08:14.449Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:08:14.449Z
---

# FloatField

FloatField 是在 Python 中由 float 实例表示的浮点数。当 localize 为 False 时是 NumberInput 否则，该字段的默认表单部件是 TextInput。

```python
field_name = models.FloatField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.FloatField()
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
                ('geeks_field', models.FloatField()),
            ],
        ),
    ]
```

## 如何使用 FloatField？

```python
from geeks.models import GeeksModel 
  
# 创建一个 float 对象
d = float(21.89) 
  
# 创建模型对象并保存 
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![floatfield.png](/assets/web框架/django/模型字段/floatfield.png)
