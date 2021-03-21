---
title: FilePathField
description: 
published: true
date: 2021-03-21T16:01:35.174Z
tags: 
editor: markdown
dateCreated: 2021-03-21T16:01:08.598Z
---

# FilePathField

文件列表显示字段，该字段接收一个必选参数以及一个可选参数。

```python
field_name = models.FilePathField（path = None，match = None，recursive= False，max_length = 100，** options）
```

- 必选参数
	- path：当前路径下的文件将会显示在下拉列表中。如：`"/home/images"`。其次 path 也可以是一个可调用对象，可以是在运行时动态设置路径的函数。例如：
  
  ```python
  import os
  from django.conf import settings
  from django.db import models

  def images_path():
      return os.path.join(settings.LOCAL_FILE_DIR, 'images')

  class MyModel(models.Model):
      file = models.FilePathField(path=images_path)
  ```
  
- 可选参数
	- `match`：一段正则表达式，用于过滤 `FilePathField.path` 中的文件，注意该正则表达式只能过滤文件名，不能过滤路径
  - `recursive`：参数值为 True 或 False。当参数设置为 True 时，`FilePathField.path` 的子文件夹中符合条件的文件也会显示在下拉列表中，否则只显示当前文件夹中的文件，默认值为 False 
  - `allow_files`：参数值为 True 或 False 
  
> 文件的绝对路径会显示在 HTML 代码中，会导致一定的安全隐患，因此需要谨慎使用 FilePathField 对象。
> FilePathField 在数据库中使用 varchar 类型存储，默认最大长度为 100 字符
{.is-warning}

```python
FilePathField(path ="/home/images", match ="foo.*", recursive = True) 
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(Model): 
	geeks_field = models.FilePathField(path ="/home/ydongy/") 
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
                ('geeks_field', models.FilePathField(path='/home/ydongy/')),
            ],
        ),
    ]
```

![filepathfield.png](/assets/web框架/django/模型字段/filepathfield.png)
