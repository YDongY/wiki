---
title: BooleanField
description: 
published: true
date: 2021-03-21T09:03:48.650Z
tags: 
editor: markdown
dateCreated: 2021-03-21T09:03:48.650Z
---

# BooleanField

BooleanField 是一个 true / false 字段。此字段的默认表单窗口小部件为 CheckboxInput ，如果 null=True 则是 NullBooleanSelect。

```python
field_name = models.BooleanField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.BooleanField()
    geeks_field2 = models.BooleanField(null=True)
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
                ('geeks_field', models.BooleanField()),
                ('geeks_field2', models.BooleanField(null=True)),
            ],
        ),
    ]
```

![booleanfield.png](/assets/web框架/django/模型字段/booleanfield.png)

# 字段选项

- null
- blank
- db_column
- default
- help_text
- primary_key
- editable
- error_messages
- help_text
- verbose_name
- validators
- unique
