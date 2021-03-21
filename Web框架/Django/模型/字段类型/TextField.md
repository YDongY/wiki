---
title: TextField
description: 
published: true
date: 2021-03-21T13:02:04.162Z
tags: 
editor: markdown
dateCreated: 2021-03-21T12:08:55.394Z
---

# TextField

TextField 是用于大型文本的大文本字段。TextField 通常用于存储段落和所有其他文本数据。该字段的默认表单窗口小部件是 TextArea。

```python
field_name = models.TextField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.TextField()
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
                ('geeks_field', models.TextField()),
            ],
        ),
    ]
```

![textfield.png](/assets/web框架/django/模型字段/textfield.png)
