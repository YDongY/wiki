---
title: Python 操作 memcached
description: 
published: true
date: 2021-03-23T15:17:26.134Z
tags: 
editor: markdown
dateCreated: 2021-03-23T15:16:57.054Z
---

# 安装

```shell
$ pip install python-memcached
```

# 快速上手

```python
import memcache

mc = memcache.Client(['127.0.0.1:11211', '192.168.10.102:11211'], debug=True)

# 设置数据
mc.set('username', 'hello world', time=60 * 5)
mc.set_multi({'email': 'admin@qq.com', 'telephone': '111111'}, time=60 * 5)

# 获取数据
print(mc.get('telephone'))

# 删除数据
mc.delete('email')  # 111111

# 自增长
mc.incr('read_count')

# 自减少
mc.decr('read_count')
print(mc.get('read_count'))  # None
```