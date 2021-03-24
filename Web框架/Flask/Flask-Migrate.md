---
title: Flask-Migrate
description: 
published: true
date: 2021-03-24T14:56:38.119Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:56:38.119Z
---

# Flask-Migrate

> https://flask-migrate.readthedocs.io/en/latest/

## 安装

```python
pip install flask-migrate -i https://pypi.doubanio.com/simple
```

## 在 manage.py 代码中使用

```python
from flask_script import Manager
from flask_migrate import Migrate,MigrateCommand
from xxx import app
from xxx import models # 需要导入模型，产生映射关系，否则无法生成迁移文件

manager = Manager(app)

# 用来绑定app和db到flask_migrate的
Migrate(app,db)

# 添加Migrate的所有子命令到db下
manager.add_command("db",MigrateCommand)

if __name__ == '__main__':
    manager.run()
```

## flask_migrate 常用命令

1. 初始化一个环境：`python manage.py db init`
2. 自动检测模型，生成迁移脚本：`python manage.py db migrate`
3. 将迁移脚本映射到数据库中：`python manage.py db upgrade`
4. 更多命令：`python manage.py db --help`

