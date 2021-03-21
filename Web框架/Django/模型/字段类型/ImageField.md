---
title: ImageField
description: 
published: true
date: 2021-03-21T13:06:15.916Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:14:35.564Z
---

# ImageField

ImageField 是一个 FileField，其上载仅限于图像格式。此字段的默认表单窗口小部件是 ClearableFileInput。

除了可用于 FileField 的特殊属性外，ImageField 还具有height 和 width 属性。其次 ImageField 需要 Pillow 库才能够正常运行

```python
field_name = models.ImageField（upload_to = None，height_field = None，width_field = None，max_length = 100，** options）
```

ImageField 具有以下可选参数：

- upload_to：与 FileField 相同
- height_field：每次保存模型实例时，模型字段的名称都会自动填充图像的高度。
- width_field：每次保存模型实例时，模型字段的名称都会自动填充图像的宽度。

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.ImageField()
```

然后再配置文件中注册应用，并执行迁移命令，生成成迁移文件如下：

```python
from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='GeeksMode',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('geeks_field', models.FileField(upload_to='')),
            ],
        ),
    ]
```

![imagefield.png](/assets/web框架/django/模型字段/imagefield.png)
