---
title: Celery 基本使用
description: 
published: true
date: 2021-02-21T13:36:31.655Z
tags: 
editor: undefined
dateCreated: 2021-02-21T09:49:13.736Z
---

# 什么是Celery

Celery是一个简单、灵活且可靠的，处理大量消息的分布式系统，专注于实时处理的异步任务队列，同时也支持任务调度。

<div style="text-align: center">
	<img src="/downloads/tools/celery1.png" class="rounded shadow"/>
</div>

上面的图是一个利用`Celery`进行异步处理的架构，属于生产者－消费者模式，架构由三部分组成：

**消息中间件（message broker）**：保存生产者生产消息或者任务的队列，通常可以使用第三方消息中间件集成，包括，`RabbitMQ`,` Redis`,`Amazon SQS`。

**任务执行单元（worker）**：是Celery提供任务执行的，可以看作是消费者消费任务的单元。

**任务执行结果存储（task result store）**：用来存储Worker执行的任务的结果，Celery支持以不同方式存储任务的结果，包括`AMQP,redis,Elasticsearch,`等。

另外，Celery还支持不同的并发和序列化的手段

- 并发：`Prefork(multiprocessing), Eventlet, gevent, solo (single threaded)`
- 序列化：`pickle, json, yaml, msgpack. zlib, bzip2 compression， Cryptographic message signing `等等

# 安装Celery

```shell
pip install -U Celery
```

如果需要特定的支持还需要安装对应的依赖包，比如：

并发依赖：`eventlet,gevent`等

消息中间件：`redis,RabbitMQ`等

结果存储：`sqlalchemy,django,elasticsearch`等

# Celery执行异步任务

文件目录结构

```shell
└── simple_test
    ├── celery_task.py
    ├── produce.py
    └── result.py
```

## １．准备消费者，创建文件`celery_task.py`

```python
import celery
import time

# 结果存储redis
backend = 'redis://127.0.0.1:6379/14'

# 中间件，此出使用redis
broker = 'redis://127.0.0.1:6379/15'

# 第一个参数Celery是当前模块的名称。这仅是必需的，以便在__main__模块中定义任务时可以自动生成名称。
# 第二个参数是broker关键字参数，指定要使用的消息代理的URL
# 第三个参数是backend关键字参数，指定消息处理结果代理的URL
cel = celery.Celery('test', broker=broker,backend=backend)

@cel.task
def send_email(name):
    """
    模拟一个发送邮件的耗时任务
    """
    print("开始发送邮件至[{name}]".format(name=name))
    time.sleep(5)
    print("发送邮件完成")
    return "ok"

@cel.task
def send_message(name):
    """
    模拟一个发送短信的耗时任务
    """
    print("开始发送短信至[{name}]".format(name=name))
    time.sleep(5)
    print("发送短信完成")
    return "ok"
```

## ２．准备生产者，创建文件`produce.py`

```python
# 导入发送邮件和短信的函数
from simple_test.celery_task import send_email, send_message

# 通过delay()方法调用任务
result = send_email.delay("ydongy-email")
print(result.id)
result2 = send_message.delay("ydongy-message")
print(result2.id)
```

## ３．运行 Celery Worker 服务器：

```bash
celery worker -A celery_task -l info
# 或者
celery worker -A celery_task -l info -P eventlet
```

<div style="text-align: center">
	<img src="/downloads/tools/celery2.png" class="rounded shadow"/>
</div>

## ４．调用任务执行`produce.py`文件

<div style="text-align: center">
	<img src="/downloads/tools/celery3.png" class="rounded shadow"/>
</div>

## ５．查看任务执行结果，创建`tasks_result.py`

```python
from celery.result import AsyncResult

from simple_test.celery_task import cel

# id为调用任务返回的id，由于任务执行是异步的，即id会在调用delay()离开返回
async_result = AsyncResult(id="0c38844f-dd87-4d67-b2f2-4af4533956cc", app=cel)

if async_result.successful():
    result = async_result.get()
    print(result)
    # result.forget() # 将结果删除
    # async_result.revoke(terminate=True)  # 无论什么时候，都要终止
    # async_result.revoke(terminate=False)  # 如果任务还没开始就终止
elif async_result.failed():
    print('执行失败')
elif async_result.status == 'PENDING':
    print('任务等待中被执行')
elif async_result.status == 'RETRY':
    print('任务异常后正在重试')
elif async_result.status == 'STARTED':
    print('任务已经开始被执行')
```

# Celery执行定时任务

目录结构

```shell
.
├── celery.py
├── __init__.py
├── task01.py
└── task02.py
```

设定时间让celery执行一个定时任务，创建`celery.py`

```python
"""run command
celery worker -A celery_tasks -l info -P eventlet
# -c 并发数
celery worker -A celery_tasks -l info -P eventlet -c 5

# 1.启动 Beat 程序
# Celery Beat进程会读取配置文件的内容，周期性的将配置中到期需要执行的任务发送给任务队列
celery beat -A celery_tasks

# 2.之后启动 worker 进程
celery -A celery_tasks worker -l info
或者
celery -B -A proj worker -l info
"""
from datetime import timedelta
from celery import Celery
from celery.schedules import crontab

cel = Celery('celery_demo',
             broker='redis://127.0.0.1:6379/15',
             backend='redis://127.0.0.1:6379/14',
             # 包含以下两个任务文件，去相应的py文件中找任务，对多个任务做分类
             include=['celery_tasks.task01',
                      'celery_tasks.task02'
                      ])
# 时区
cel.conf.timezone = 'Asia/Shanghai'
# 是否使用UTC
cel.conf.enable_utc = False

# 定时任务
cel.conf.beat_schedule = {
    # 名字随意命名
    'add-every-6-seconds': {
        # 执行tasks1下的test_celery函数
        'task': 'celery_tasks.task01.send_email',
        # 每隔1秒执行一次
        # 'schedule': 1.0,
        # 'schedule': crontab(minute="*/1"),
        'schedule': timedelta(seconds=6),
        # 传递参数
        'args': ('张三',)
    },
    # 'add-every-12-seconds': {
    #     'task': 'celery_tasks.task01.send_email',
    #     每年4月11号，8点42分执行
    #     'schedule': crontab(minute=42, hour=8, day_of_month=11, month_of_year=4),
    #     'args': ('张三',)
    # },
}
```

创建两个任务文件`task01,task02`

```python
#===============task01.py=======
from celery_tasks.celery import cel
import time

@cel.task
def send_email(name):
    print("开始发送邮件至[{name}]".format(name=name))
    time.sleep(5)
    print("发送邮件完成")
    return "ok"

#===============task02.py=======
from celery_tasks.celery import cel
import time

@cel.task
def send_message(name):
    print("开始发送短信至[{name}]".format(name=name))
    time.sleep(5)
    print("发送短信完成")
    return "ok"
```

启动程序：

```shell
# 启动消费者，worker
celery -A celery_tasks worker -l info
# 启动定时任务，不断生产任务到borker消息中间件，等待处理
celery beat -A celery_tasks
```

# Django 中使用 Celery

项目目录结构

<div style="text-align: center">
	<img src="/downloads/tools/celery4.png" class="rounded shadow"/>
</div>

配置`config.py`

```python
from celery.schedules import crontab
from datetime import timedelta

broker_url = 'redis://127.0.0.1:6379/15'
result_backend = 'redis://127.0.0.1:6379/14'

imports = ('mycelery.email.tasks', 'mycelery.sms.tasks')

timezone = 'Asia/Shanghai'
enable_utc = False

# 定时任务
beat_schedule = {
    # Executes every Monday morning at 7:30 a.m.
    # 'add-every-monday-morning': {
    #     'task': 'mycelery.sms.tasks.send_sms',
    #     'schedule': crontab(hour=7, minute=30, day_of_week=1),
    #     'args': ("ydongy",),
    # },
    'add-every-6-seconds': {
        # 执行tasks1下的test_celery函数
        'task': 'celery_tasks.task01.send_email',
        # 每隔1秒执行一次
        # 'schedule': crontab(minute="*/1"),
        'schedule': timedelta(seconds=6),
        # 传递参数
        'args': ('张三',)
    },
}
```

> [更多配置](https://docs.celeryproject.org/en/stable/userguide/configuration.html#configuration)
{.is-info}


`main`程序配置`Celery`程序

```python
# 主程序
import os
import django
from celery import Celery

# 创建celery实例对象
app = Celery("djCelery")

# 把celery和django进行组合，识别和加载django的配置文件
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'djCelery.settings')

django.setup() # 不添加可能会导致找不到 DJANGO_SETTINGS_MODULE

# 通过app对象加载配置
app.config_from_object("mycelery.config")

# 加载任务
# 参数必须必须是一个列表，里面的每一个任务都是任务的路径名称
# app.autodiscover_tasks(["任务1","任务2"])
app.autodiscover_tasks(["mycelery.email","mycelery.sms" ])

# 启动Celery的命令
# 强烈建议切换目录到mycelery根目录下启动
# celery -A mycelery.main worker --loglevel=info
```

分别在`email和sms`下的`tasks`文件添加任务代码，**注意：每个任务目录下的任务文件名必须是`tasks.py`**

```python
from mycelery.main import app
import time

@app.task
def send_message(name):
    print("开始发送短信至[{name}]".format(name=name))
    time.sleep(5)
    print("发送短信完成")
    return "ok"
```

视图函数调用执行

```python
from django.shortcuts import render, HttpResponse
from mycelery.email.tasks import send_message
from datetime import timedelta,datetime

def test(request):
    # 异步任务
    # 1. 声明一个和celery一模一样的任务函数，但是我们可以导包来解决
    send_message.delay("ydongy")
    # send_sms.delay() 如果调用的任务函数没有参数，则不需要填写任何内容

    #  定时任务，规定某个时间点执行
    # ctime = datetime.now()
    # # 默认用utc时间
    # utc_ctime = datetime.utcfromtimestamp(ctime.timestamp())
    # time_delay = timedelta(seconds=10)
    # task_time = utc_ctime + time_delay
    # result = send_sms.apply_async(["911", ], eta=task_time)
    # print(result.id)

    return HttpResponse('ok')
```

# 相关参考：

- https://docs.celeryproject.org/en/stable/index.html
- https://www.cnblogs.com/pyedu/p/12461819.html