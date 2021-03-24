---
title: Flask-Mail
description: 
published: true
date: 2021-03-24T15:08:42.457Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:08:17.873Z
---

# Flask-Mail

> https://pythonhosted.org/Flask-Mail/
{.is-success}


## 安装

```shell
pip install flask-mail
```

## 定义使用

```python
from flask import Flask
from flask_mail import Mail

app = Flask(__name__)
mail = Mail(app)
```

> 更多使用参考[官方文档](https://pythonhosted.org/Flask-Mail/)
{.is-success}


| 配置键              | 说 明                      | 默认值    |
| ------------------- | -------------------------- | --------- |
| MAIL_SERVER         | 用于发送邮件的 SMTP 服务器 | localhost |
| MAIL_PORT           | 发送端口                   | 25        |
| MAIL_USE_TLS        | 是否使用 STARTTLS          | False     |
| MAIL_USE_SSL        | 是否使用 SSL/TLS           | False     |
| MAIL_USERNAME       | 发信服务器的用户名         | None      |
| MAIL_PASSWORD       | 发信服务器的密码           | None      |
| MAIL_DEFAULT_SENDER | 默认的发信人               | None      |
