---
title: EmailField
description: 
published: true
date: 2021-03-21T10:53:48.234Z
tags: 
editor: markdown
dateCreated: 2021-03-21T10:53:48.234Z
---

# EmailField

EmailField 是一个 CharField，它使用 EmailValidator 检查有效电子邮件地址的值。EmailValidator 通过预定义的正则表达式验证电子邮件。

```python
field_name = models.EmailField（max_length = 254，** options）
```

EmailField 有一个额外的必需参数：

- max_length：字段的最大长度（以字符为单位）

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.EmailField(max_length=254)
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
                ('geeks_field', models.EmailField(max_length=254)),
            ],
        ),
    ]
```

## 如何使用 EmailField？

```python
from geeks.models import GeeksModel 
  
# 新建一个邮箱字符串
d = "admin@gmail.com"
  
# 创建模型对象实例并保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![emailfield.png](/assets/web框架/django/模型字段/emailfield.png)

