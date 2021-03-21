---
title: BigAutoField
description: 
published: true
date: 2021-03-21T09:04:41.157Z
tags: 
editor: markdown
dateCreated: 2021-03-21T08:34:34.417Z
---

# BigAutoField

BigAutoField 是 64 位整数，类似于 AutoField，它保证可以容纳 1 到 9223372036854775807 之间的数字。

可以通过下面的语法创建：

```python
id = models.BigAutoField（primary_key = True，** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
from django.db import models 
from django.db.models import Model 
# Create your models here. 

class GeeksModel(Model): 
	big_id = models.BigAutoField(primary_key = True) 
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
                ('big_id', models.BigAutoField(primary_key=True, serialize=False)),
            ],
        ),
    ]
```

> Django 模型最多可以具有一个自动字段，所以必须在第一次迁移时使用 primary_key=True 属性创建 BigAutoField
{.is-warning}

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
