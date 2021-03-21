---
title: FileField
description: 
published: true
date: 2021-03-21T15:48:29.831Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:01:40.177Z
---

# FileField

FileField 是一个文件上传字段。此字段的默认表单窗口小部件是 ClearableFileInput。

> 该字段不允许使用 primary_key 属性
{.is-warning}


```python
field_name = models.FileField（upload_to = None，max_length = 254，** options）
```

FileField 有一个可选参数：

- upload_to：文件上传后的保存文职

```python
class MyModel(models.Model): 

	# ＃文件上传到 MEDIA_ROOT/uploads
	upload = models.FileField(upload_to ='uploads/') 

	# ＃文件上传到 MEDIA ROOT/uploads/2017/12/30
	upload = models.FileField(upload_to ='uploads/%Y/%m/%d/') 
```

> MEDIA_ROOT 在 settings.py 中设置，upload_to 所指定的路径将会拼接在 MEDIA_ROOT 之后 。
{.is-info}

upload_to 也可以是可调用的函数，且必须接受两个参数，并返回一个 Unix 风格的路径（带斜线）

```python
def user_directory_path(instance, filename): 

	# file will be uploaded to MEDIA_ROOT / user_<id>/<filename> 
	return 'user_{0}/{1}'.format(instance.user.id, filename) 

class MyModel(models.Model): 
	upload = models.FileField(upload_to = user_directory_path) 
```

- storage：负责文件存储的 Python 类，用于存储和提取文件。类 `django.core.files.storage.FileSystemStorage` 提供了基本的文件管理功能。例如下面代码，无论在 settings.py 里面如何设置 MEDIA_ROOT 都会按照所指定的路径存储被上传的文件：

```python
from django.core.files.storage import FileSystemStorage

fs = FileSystemStorage(location='/meida/documents')

class GeeksModel5(models.Model):
    photo = models.FileField(storage=fs)
```

# 案例 

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.FileField()
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
                ('geeks_field', models.FileField(upload_to='')),
            ],
        ),
    ]
```

![filefield.png](/assets/web框架/django/模型字段/filefield.png)
