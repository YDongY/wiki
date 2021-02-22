---
title: requests
description: 
published: true
date: 2021-02-22T10:38:32.413Z
tags: python, requests, 库
editor: markdown
dateCreated: 2021-02-22T10:38:32.413Z
---

# requests

> [官方文档](https://requests.readthedocs.io/en/master/)
{.is-info}


## GET 请求

```python
import requests

r = requests.get("https://api.github.com/events")

print(r.status_code)  # 200
print(r.headers['content-type'])  # application/json; charset=utf-8
# print(r.text)
print(r.encoding)  # utf-8
print(r.json())

# 二进制内容
print(r.content)

''' eg: 保存二进制内容（图片）
from PIL import Image
from io import BytesIO
i = Image.open(BytesIO(r.content))
'''

# 原始内容

r = requests.get('https://api.github.com/events', stream=True)
print(r.raw)
print(r.raw.read(10))

# 传递 URL 参数

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get("http://httpbin.org/get", params=payload)
print(r.url)  # http://httpbin.org/get?key1=value1&key2=value2

payload2 = {'key1': 'value1', 'key2': ['value2', 'value3']}
r = requests.get('https://httpbin.org/get', params=payload)
print(r.url) # https://httpbin.org/getkey1=value1&key2=value2&key2=value3

# 定制请求头

user_agent = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36'

headers = {
    'User-Agent': user_agent
}

r = requests.get("http://httpbin.org/get", headers=headers)

```

## POST 请求

```python

# HTML表单编码

payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post("https://httpbin.org/post", data=payload)
print(r.text)

# JSON编码
#   方式一：data

import json
payload = {'some': 'data'}
r = requests.post('https://api.github.com/some/endpoint', data=json.dumps(payload))

#   方式二：json

payload = {'some': 'data'}
r = requests.post('https://api.github.com/some/endpoint', json=payload)

# 文件上传

files = {'file': open('report.xls', 'rb')}
r = requests.post('https://httpbin.org/post', files=files)
```

# Cookie

```python
cookies = dict(cookies_are='working')
r = requests.get('https://httpbin.org/cookies', cookies=cookies)
```








