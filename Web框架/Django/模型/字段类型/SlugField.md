---
title: SlugField
description: 
published: true
date: 2021-03-21T13:01:22.395Z
tags: 
editor: markdown
dateCreated: 2021-03-21T12:01:03.044Z
---

# SlugField

SlugField 是某物的简短标签，仅包含字母，数字，下划线或连字符。

```python
field_name = models.SlugField（max_length = 200，** options）
```

通常在 URL 中使用。例如，在典型的博客条目 URL 中：

```http
https://www.geeksforgeeks.org/add-the-slug-field-inside-django-model/
```

在这里，最后一个数据 `add-the-slug-field-inside-django-model` 是 slug

SlugField 具有以下可选参数：

- `max_length`：默认长度 50
- `allow_unicode`：如果为 True，则该字段除了接受 ASCII 字母外，还接受 Unicode 字母。默认为 False。


# 案例


创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.SlugField(max_length=200)
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
                ('geeks_field', models.SlugField(max_length=200)),
            ],
        ),
    ]
```


![slugfield.png](/assets/web框架/django/模型字段/slugfield.png)
