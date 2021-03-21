---
title: DecimalField
description: 
published: true
date: 2021-03-21T12:56:22.993Z
tags: 
editor: markdown
dateCreated: 2021-03-21T10:39:07.549Z
---

# DecimalField

DecimalField 是固定精度的小数，在 Python 中使用 Decimal 实例表示。它使用 DecimalValidator 验证输入。

```python
field_name = models.DecimalField（max_digits = None，decimal_places = None，** options）
```

DecimalField 具两个必须的参数：

- `max_digits`：数字中允许的最大位数
- `decimal_places`：存储的小数位数

例如：存储 5 位数字，其中小数位数为 2 位

```python
models.DecimalField（...，max_digits = 5，decimal_places = 2）
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel2(models.Model):
    geeks_field = models.DecimalField(max_digits=5, decimal_places=2)
```

然后再配置文件中注册应用，并执行迁移命令，生成成迁移文件如下：

```python
from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='GeeksModel2',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('geeks_field', models.DecimalField(decimal_places=2, max_digits=5)),
            ],
        ),
    ]
```

## 如何使用 DecimalField？

```python
from geeks.models import GeeksModel
import decimal

# 创建一个 decimal.Decimal 对象
d = decimal.Decimal(9.53) 

# 创建一个模型对象进行保存
geek_object = GeeksModel.objects.create(geeks_field = d) 
geek_object.save() 
```

![decimalfield.png](/assets/web框架/django/模型字段/decimalfield.png)

