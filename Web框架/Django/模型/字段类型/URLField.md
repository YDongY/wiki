---
title: URLField
description: 
published: true
date: 2021-03-21T12:21:48.383Z
tags: 
editor: markdown
dateCreated: 2021-03-21T12:21:48.383Z
---

# URLField

URLField 是 URL 的 CharField 。它通常用于存储网页链接，由 URLValidator 验证

```python
field_name = models.URLField（max_length = 200，** options）
```

URLField 有一个额外的可选参数：

- max_length：字段的最大长度（以字符为单位），默认值200。

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.URLField(max_length=200)
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
                ('geeks_field', models.URLField()),
            ],
        ),
    ]
```

![urlfield.png](/assets/web框架/django/模型字段/urlfield.png)

