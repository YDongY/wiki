---
title: FileField
description: 
published: true
date: 2021-03-21T12:57:33.129Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:01:40.177Z
---

# FileField

FileField 是一个文件上传字段。此字段的默认表单窗口小部件是    ClearableFileInput。

```python
field_name = models.FileField（upload_to = None，max_length = 254，** options）
```

FileField 有一个可选参数：

- upload_to：提供了一种设置上传目录和文件名的方法

```python
class MyModel(models.Model): 

	# file will be uploaded to MEDIA_ROOT / uploads 
	upload = models.FileField(upload_to ='uploads/') 

	# or... 
	# file will be saved to MEDIA_ROOT / uploads / 2015 / 01 / 30 
	upload = models.FileField(upload_to ='uploads/% Y/% m/% d/') 
```

> 使用默认的 `FileSystemStorage` ，则字符串值将附加到 MEDIA_ROOT 路径中，以形成本地文件系统上将存储上传文件的位置
{.is-info}


upload_to 也可以是可调用的，例如函数：

```python
def user_directory_path(instance, filename): 

	# file will be uploaded to MEDIA_ROOT / user_<id>/<filename> 
	return 'user_{0}/{1}'.format(instance.user.id, filename) 

class MyModel(models.Model): 
	upload = models.FileField(upload_to = user_directory_path) 

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
