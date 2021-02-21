---
title: alembic 数据库迁移工具
description: 
published: true
date: 2021-02-21T13:36:34.823Z
tags: 
editor: undefined
dateCreated: 2021-02-21T09:55:57.941Z
---

# 安装
```shell
pip install alembic -i https://pypi.doubanio.com/simple
```

# 使用步骤

## 1.定义模型

```python
from sqlalchemy import Column, String, Integer, create_engine
from sqlalchemy.ext.declarative import declarative_base

HOSTNAME = '127.0.0.1'
PORT = '3306'
DATABASE = 'alembic'
USERNAME = 'root'
PASSWORD = 'root'
DB_URI = f"mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8"

#　创建引擎
engine = create_engine(DB_URI)

Base = declarative_base(engine)

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(length=50), nullable=False)
    age = Column(Integer, nullable=False)
```
1. `engine = create_engine(DB_URI)`：创建引擎，相当于连接到数据库
2. `Base = declarative_base(engine)`：绑定引擎
3. `class User(Base):`：定义模型关联`Base`


## 2. 创建仓库

> `alembic init [仓库的名字，推荐使用alembic]`

```shell
# ydongy @ ydongy-PC in ~/Code/flask_project/alembic [16:57:31]
$ alembic init alembic
  Creating directory /home/ydongy/Code/flask_project/alembic/alembic ...  done
  Creating directory /home/ydongy/Code/flask_project/alembic/alembic/versions ...  done
  Generating /home/ydongy/Code/flask_project/alembic/alembic/README ...  done
  Generating /home/ydongy/Code/flask_project/alembic/alembic/script.py.mako ...  done
  Generating /home/ydongy/Code/flask_project/alembic/alembic.ini ...  done
  Generating /home/ydongy/Code/flask_project/alembic/alembic/env.py ...  done
  Please edit configuration/connection/logging settings in '/home/ydongy/Code/flask_project/alembic/alembic.ini' before proceeding.
(flask_project)
# ydongy @ ydongy-PC in ~/Code/flask_project/alembic [16:57:46]
$
```

假如输入命令`alembic init alembic`，会在当前的目录创建一个`alembic`的文件夹，层级关系以及文件结构如下：
```python
.
├── alembic
│   ├── env.py
│   ├── README
│   ├── script.py.mako
│   └── versions
├── alembic.ini
├── models.py
```

## 3.修改配置文件
- 修改`alembic.ini`:

```python
# sqlalchemy.url = driver://user:pass@localhost/dbname
sqlalchemy.url = mysql+pymysql://root:root@localhost/demo?charset=utf8
```
根据自己使用的数据库配置数据库的相关信息，以及数据库连接引擎，此处使用的是`mysql+pymysql`

- 修改`alembic/env.py`

```python
import sys,os
# 添加模块查找路径
sys.path.append(os.path.dirname(os.path.dirname(__file__)))

# target_metadata = None
target_metadata = Base.metadata
```
-  添加模块查找路径是要找到我们的`Base = declarative_base(engine)`
-  把`target_metadata = None`修改成`Base.metadata`

**一般这一步容易出错，大部分就是没有添加好模块路径**


## 4. 生成迁移脚本
```shell
alembic revision --autogenerate -m 'message'

# message：表示描述信息，类似于 git 提交时的描述信息
```

## 5. 将迁移脚本映射到数据库
```shell
alembic upgrade head
```

# 常用命令

|       命令       |                             描述                             |
| :--------------: | :----------------------------------------------------------: |
|      `init`      |                    创建一个alembic仓库。                     |
|    `revision`    |                    创建一个新的版本文件。                    |
| `--autogenerate` |             自动将当前模型的修改，生成迁移脚本。             |
|       `-m`       |    本次迁移做了哪些修改，用户可以指定这个参数，方便回顾。    |
|     `uprade`     | 将指定版本的迁移文件映射到数据库中，会执行版本文件中的upgrade函数。如果有多个迁移脚本没有被映射到数据库中，那么会执行多个迁移脚本。 |
|     `[head]`     |                 代表最新的迁移脚本的版本号。                 |
|   `downgrade`    |         会执行指定版本的迁移文件中的downgrade函数。          |
|     `heads`      |                展示head指向的脚本文件版本号。                |
|    `history`     |                 列出所有的迁移版本及其信息。                 |
|    `current`     |                  展示当前数据库中的版本号。                  |
