---
title: BinaryField
description: 
published: true
date: 2021-03-21T12:53:55.065Z
tags: 
editor: markdown
dateCreated: 2021-03-21T08:54:50.662Z
---

# BinaryField

BinaryField 是用于存储原始二进制数据的特殊字段。可以指定为 bytes、bytearray 或 memoryview。

默认情况下，BinaryField 将 editable 设置为 False，即不能通过任何形式编辑。

由于 BinaryField 存储原始数据或以其他方式存储 python 对象，因此无法手动输入，需要通过视图或 Django shell 进行分配

```python
field_name = models.BinaryField（** options）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.BinaryField()
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
                ('geeks_field', models.BinaryField()),
            ],
        ),
    ]
```

## 如何使用 BinaryField？

```python
from geeks.models import GeeksModel 
  
# 创建一个字符串
test_string = "Hello World"
  
# 将字符串转换成字节
res = bytes(test_string, 'utf-8') 
  
# 创建一个对象保存
geek_object = GeeksModel.objects.create(geeks_field = res) 
geek_object.save() 
```

由于 editable = False ，所以我们无法通过后台管理进行创建数据。如果尝试使用 editable=True 它来使其可编辑，则会产生意外错误

![](https://media.geeksforgeeks.org/wp-content/uploads/20191005130550/binaryfield-editable-django-models.png)
