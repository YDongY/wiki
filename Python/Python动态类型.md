---
title: Python 动态类型
description: 
published: true
date: 2021-03-05T11:45:30.928Z
tags: python
editor: markdown
dateCreated: 2021-03-05T11:34:20.411Z
---

要了解动态类型，我们先来看一个例子：

```python
>>> a = 3
>>> b = 3 * a
>>> b
9
>>> 
```

当运行上述代码时会发现，Python 是如何知道 a 代表一个整数？否则 a 不是整数就无法参与乘法运算。 要了解清楚这个问题，我们先要明白变量、对象和引用三者的关系。

# 变量、对象和引用


