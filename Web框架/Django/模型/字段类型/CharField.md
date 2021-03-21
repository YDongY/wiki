---
title: CharField
description: 
published: true
date: 2021-03-21T12:55:03.149Z
tags: 
editor: markdown
dateCreated: 2021-03-21T10:11:19.630Z
---

# CharField

CharField 是一个字符串字段，通常用于存储小字符串，例如名字，姓氏等。要存储较大的文本，请使用 TextField。此字段的默认表单窗口小部件为 TextInput。

CharField 有一个额外的必需参数：`max_length`，字段的最大长度（以字符为单位）。max_length 是在数据库层和 Django 的验证中使用 MaxLengthValidator 强制执行的 

```python
field_name = models.CharField（max_length = 200，** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.CharField(max_length=200)
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
                ('geeks_field', models.CharField(max_length=200)),
            ],
        ),
    ]
```

![charfield.png](/assets/web框架/django/模型字段/charfield.png)
