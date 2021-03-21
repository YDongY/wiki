---
title: AutoField
description: 
published: true
date: 2021-03-21T08:27:54.690Z
tags: 
editor: markdown
dateCreated: 2021-03-21T08:27:54.690Z
---

# AutoField

AutoField 是一个 IntegerField ，根据可用 ID 自动递增。默认情况下，Django 为每个模型提供以下字段：

```python
id = models.AutoField（primary_key = True，** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    pass
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
            ],
        ),
    ]
```

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










