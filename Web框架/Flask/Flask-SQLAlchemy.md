---
title: Flask-SQLAlchemy
description: 
published: true
date: 2021-03-24T14:53:31.686Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:52:39.316Z
---

# Flask-SQLAlchemy

> https://flask-sqlalchemy.palletsprojects.com/en/2.x/
{.is-success}


## 安装

```python
pip install flask-sqlalchemy -i https://pypi.doubanio.com/simple
```

## 定义使用

Flask-SQLAlchemy和原生的SQLAlchemy基本一致，首先想让我们定义一下数据库并进行连接

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)  # type:Flask

HOSTNAME = '127.0.0.1'
PORT = '3306'
DATABASE = 'flask'
USERNAME = 'root'
PASSWORD = 'root'
DB_URI = f"mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8"

app.config['SQLALCHEMY_DATABASE_URI'] = DB_URI
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app=app)


class User(db.Model):
    __tablename__ = 'user'

    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    username = db.Column(db.String(length=50), nullable=False)


class Article(db.Model):
    __tablename__ = 'article'

    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    title = db.Column(db.String(length=50), nullable=False)

    uid = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)

    author = db.relationship("User", backref="articles")


# db.drop_all()
# db.create_all()

# user = User(username="ydy")
# article = Article(title="xxxxxxaaaa")
#
# article.author = user
#
# db.session.add(article)
# db.session.commit()

users = User.query.all()
print(users)

if __name__ == '__main__':
    app.run(debug=True)
```

使用Flask-SQLAlchemy需要配置一下几个部分：

1. 导入`from flask_sqlalchemy import SQLAlchemy`
2. 配置相关数据库参数用于连接，最终形成一个URI：`DB_URI = f"mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8"`
3. 把URI添加到app的配置，`app.config['SQLALCHEMY_DATABASE_URI'] = DB_URI`，`app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False`为了防止结合`alembic`生成迁移数据库提示错误信息
4. 创建SQLAlchemy对象，`db = SQLAlchemy(app=app)`，这一步必须放在配置之后否则无效。

常用的 DBMS 及其数据库 URI 格式示例：

| DBMS            | URI                                              |
| --------------- | ------------------------------------------------ |
| PostgreSQL      | postgresqI://username:password@host/databasename |
| MySQL           | mysqI://username:password@host/databasename      |
| Oracle          | oracle://username:password@bost:port/sidname     |
| SQLite (UNIX)   | sqIite:////absolute/path/to/foo.db               |
| SQlite(内存型 ) | sqlite:/// 或 sqlite:///:memory:                 |

## CRUD

- **Create**
  - `db.session.add(obj)`
  - `db.session.add([obj1,obj2...])`
- **Read**：`<模型类>.query.<过滤方法>.<查询方法>`
  - `User.query.all()`：返回包含所有查询记录的列表
  - `User.query.first()`：返回查询的第一条记录，如果未找到，则返回 None
  - `User.query.one()`：返回第一条记录，且仅允许有一条记录 。 如果记录数量大于 1 或小于 1 ， 则抛出错误
  - `User.query.ged(ident)`：传入主键值作为参数，返回指定主键值的记录，如果未找到， 则返回 None
  - `User.query.count()`：返回查询j结果的数量
  - `User.query.one_or_none()`：类似 one() ，如果结果数量不为 1 ，返回 None
  - `User.query.first_or_404()`：返回查询的第一条记录，如果未找到，则返回 404 错误响应
  - `User.query.get_or_404(ident)`：传入主健值作为参数，返回指定主键值的记录，如果未找到，则返回 404 错误响应
  - `User.query.paginate()`：返回一个 Paginate 对象，可以对记录进行分页处理
  - `User.query.with_parent(instance)`：传人模型类实例作为参数 ， 返回和这个实例相关联的对象

过滤方法：

| 过滤器名称     | 说明                                                       |
| -------------- | ---------------------------------------------------------- |
| filter()       | `User.query.filter(User.name == 'ydongy').all()`           |
| filter_by()    | `User.query.filter_by(name='ydongy').all()`                |
| order_by()     | 根据指定条件对记录进行排序，返回新产生的查询对象           |
| limit(limit)   | 使用指定的值限制原查询返回的记录数盐，返回新产生的查询对象 |
| group_by()     | 根据指定条件对记录进行分组，返回新产生的查询对象           |
| offset(offset) | 使用指定的值偏移原查询的结果，返回新产生的查询对象         |

- **Delete**：删除记录
  - `db.session.delete(obj)`
- **Commit**：提交保存
  - `db.session.commit()`
- 重新生成表
  - `db.drop_all()`
  - `db.create_all()`

## 结合 alembic 迁移

在对应文件目录执行命令，初始化一个 alembic 的文件夹（名称随便取，一般为 alembic ）

```shell
alembic init alembic
```

文件目录如下：

```shell
.
├── alembic
│   ├── env.py
│   ├── README
│   ├── script.py.mako
│   └── versions
├── alembic.ini
└── demo.py
```

## 修改 alembic/env.py 文件配置

目的是导入我们 app 所在文件的模块路径。需要根据个人情况，每个人的目录结构不同，所以查找路径不同。例如：我的文件就是上面的 demo.py ，结合目录结构。之后还需要导入 app 所在的文件，并且修改`target_metadata`的值

```python
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(__file__)))

import demo

target_metadata = demo.db.Model.metadata
```

## 修改 alembic.ini 配置

```python
sqlalchemy.url = mysql+pymysql://root:root@localhost/demo?charset=utf8
```

## 生成迁移脚本

```python
alembic revision --autogenerate -m 'message'
```

成功之后会在 versions 文件夹下创建一个 py 文件。

基本的 Flask-SQLALchemy 配置完成，其他的操作基本和 SQLAlchemy 一致。
