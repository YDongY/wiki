---
title: Django 缓存 memcached
description: 
published: true
date: 2021-03-23T15:27:32.605Z
tags: 
editor: markdown
dateCreated: 2021-03-23T15:24:34.427Z
---


> [官方文档](https://docs.djangoproject.com/zh-hans/3.1/topics/cache/#django-s-cache-framework)
{.is-success}

# 配置

首先需要在 `settings.py` 中配置好缓存：

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
    }
}
```

如果想要使用多台机器，那么可以在LOCATION指定多个连接，示例代码如下：

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': [
            '172.19.26.240:11211',
            '172.19.26.242:11211',
        ]
    }
}
```

# 使用

配置好 memcached 的缓存后，以后在代码中就可以使用以下代码来操作 memcached 了：

```python
from django.core.cache import cache

def index(request):
    cache.set('username','张三',60)
    print(cache.get('username'))
    response = HttpResponse('index')
    return response
```


需要注意的是，django 在存储数据到 memcached 中的时候，不会将指定的 key 存储进去，而是会对key 进行一些处理。比如会加一个前缀，会加一个版本号。如果想要自己加前缀，那么可以在`settings.CACHES` 中添加 `KEY_FUNCTION` 参数：

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
        'KEY_FUNCTION': lambda key,prefix_key,version:"django:%s"%key
    }
}
```

