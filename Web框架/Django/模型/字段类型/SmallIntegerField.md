---
title: SmallIntegerField
description: 
published: true
date: 2021-03-21T13:01:39.504Z
tags: 
editor: markdown
dateCreated: 2021-03-21T12:05:15.305Z
---

# SmallIntegerField

SmallIntegerField 是在 Python 中由 int 实例表示的整数。此字段类似于 IntegerField，并且支持从 -32768 到 32767 的值。

当 localize 为 False 时，此字段的默认表单窗口小部件为 NumberInput，否则为 TextInput。它使用 MinValueValidator 和 MaxValueValidator 根据默认数据库支持的值来验证输入。

```python
field_name = models.SmallIntegerField(** options)
```

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.SmallIntegerField()
```

然后再配置文件中注册应用，并执行迁移命令，生成成迁移文件如下：

```python
class Migration(migrations.Migration):

    dependencies = [
    ]

    operations = [
        migrations.CreateModel(
            name='GeeksModel',
            fields=[
                ('id', models.AutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('geeks_field', models.SmallIntegerField()),
            ],
        ),
    ]

```

![smallintegerfield.png](/assets/web框架/django/模型字段/smallintegerfield.png)
