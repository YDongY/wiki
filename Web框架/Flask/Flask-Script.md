---
title: Flask-Script
description: 
published: true
date: 2021-03-24T14:55:16.102Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:55:16.102Z
---

# Flask-Script

> https://flask-script.readthedocs.io/en/latest/
{.is-success}


## 安装

```python
pip install flask-script -i https://pypi.doubanio.com/simple
```

## 定义使用

新建一个文件`manager.py`，输入一下格式代码

```python
from flask_script import Manager
from xxx import app # 从其他文件导入app

manager = Manager(app) # 接受用一个app

if __name__ == '__main__':
    manager.run()
```

添加脚本

```python
@manager.option("-u", "--name", dest="name")
def add_user(name):
    print("hello %s" % name)
```

添加一个函数，接受一个个参数，并且添加了一个个装饰器，接着我们通过命令执行文件，**其中`dest`是接受传递的值**:

```python
python manager.py add_user -u "ydy"
或者
python manager.py add_user --name "ydy"
```

## 使用子命令

我们可以新建一个 `db_script.py` 来管理我们操作数据库的命令文件

```python
from flask_script import Manager

db_manager = Manager()

@db_manager.command
def init():
    pass

@db_manager.command
def makemigrations():
    pass

@db_manager.command
def migrate():
    pass
```

之后我们在 script 主文件注册子命令：

```python
from flask_script import Manager
from xxx import app # 从其他文件导入app
from db_script import db_manager

manager = Manager(app) # 接受用一个app
manager.add_command("db",db_manager) # 添加子命令，名称为db，随意取

if __name__ == '__main__':
    manager.run()
```

使用子命令：

```python
python manager.py db init
python manager.py db makemigrations
python manager.py db migrate
```

## 使用类定义

举一个非常简单的例子，我们想创建一个 hello 仅打印出 “hello world” 的命令。它不需要任何参数，因此非常简单：

```python
from flask_script import Command

class Hello(Command):
    "prints hello world"

    def run(self):
        print "hello world"

manager.add_command('hello', Hello())
```

使用类定义：

- 必须继承 Command
- 必须实现 run 方法
- 必须通过 add_command 方法添加命令

**通过类定义接受参数的形式：**

```python
from flask_script import Command,Option

class Hello(Command):
    option_list = (
        Option("-u", "--name", dest="name"),
    )

    def run(self, name):
        print("hello {}".format(name))

manager.add_command("hello", Hello())

#========================方式二=======================
class Hello2(Command):
    def __init__(self):
        # 一般用来设置默认值
        pass

    def get_options(self):
        return [
            Option("-u", "--name", dest="name"),
        ]

    def run(self, name):
        print("hello {}".format(name))

manager.add_command("hello2", Hello2())
```
