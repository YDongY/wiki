---
title: GenericIPAddressField
description: 
published: true
date: 2021-03-21T12:58:51.432Z
tags: 
editor: markdown
dateCreated: 2021-03-21T11:30:02.510Z
---

# GenericIPAddressField

GenericIPAddressField 是一个以字符串格式存储 IPv4 或 IPv6 地址的字段。该字段的默认表单窗口小部件是 TextInput

```python
field_name = models.GenericIPAddressField（** options）
```

GenericIPAddressField 接受以下参数：

- `protocol`：指定的协议。`both`(默认)，`IPv4` 或 `IPv6`。匹配不区分大小写。
- `unpack_ipv4`：解压缩 IPv4 映射的地址，例如 `::ffff:192.0.2.1`。如果启用此选项，则该地址将解压缩为 `192.0.2.1` 。默认设置为禁用。只能在协议设置为 `both` 时使用

# 案例

创建一个应用，在 models 下面添加以下代码：

```python
class GeeksModel(models.Model):
    geeks_field = models.GenericIPAddressField()
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
                ('geeks_field', models.GenericIPAddressField()),
            ],
        ),
    ]

```

![genericipaddressfield.png](/assets/web框架/django/模型字段/genericipaddressfield.png)

