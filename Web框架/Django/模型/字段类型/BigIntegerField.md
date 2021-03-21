---
title: BigIntegerField
description: 
published: true
date: 2021-03-21T08:42:57.931Z
tags: 
editor: markdown
dateCreated: 2021-03-21T08:42:12.080Z
---

# BigIntegerField

BigIntegerField 是 64 位整数，类似于 IntegerField。它可以容纳 -9223372036854775808 到 9223372036854775807的数字。此字段的默认表单窗口小部件是 TextInput。

```python
field_name = models.BigIntegerField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.BigIntegerField()
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
                ('geeks_field', models.BigIntegerField()),
            ],
        ),
    ]
```

![](https://media.geeksforgeeks.org/wp-content/uploads/20191005114924/bigintegerfield-django-models-1024x226.png)

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
