---
title: collections
description: 
published: true
date: 2021-03-14T06:50:01.816Z
tags: 
editor: markdown
dateCreated: 2021-03-14T06:50:01.816Z
---

# namedtuple -- 带命名的元组

```python
from collections import namedtuple

Point = namedtuple('Ponit', ['x','y'])
p = Point(10, y=20)

print(p)           # Ponit(x=10, y=20)

print(p.x + p.y)   # 30

print(p[0] + p[1]) # 30

x, y = p
print(x,y)         # 10, 20
```

- 欧式距离：(x~1~ - x~2~)^2^ + (y~1~ - y~2~)^2^ + (y~1~ - y~2~)^2^

```python
from collections import namedtuple
from math import sqrt
Point = namedtuple('Ponit', ['x','y','z'])

class Vector(Point):
    def __init__(self, x, y, z):
        super(Vector).__init__()
        self.x = x
        self.y = y
        self.z = z
    
    def __sub__(self, other):
        tmp = (self.x - other.x)**2+(self.y - other.y)**2+(self.z - other.z)**2
        return sqrt(tmp)

p1 = Vector(1, 2, 3)
p2 = Vector(4, 5, 6)

p1-p2 # 5.196152422706632
```


# deque -- 双端队列

```python
from collections import deque

d = deque('uvw')
print(d) # deque(['u', 'v', 'w'])

d.append('xyz')
print(d) # deque(['u', 'v', 'w', 'xyz'])

d.appendleft('rst')
print(d) # deque(['rst', 'u', 'v', 'w', 'xyz'])

```

# Counter -- 计数器

- 取得频率最高的前三个值

```python
from collections import Counter

mystring = ['a','b','c','d','d','d','d','c','c','e']

cnt = Counter(mystring)
print(cnt) # Counter({'d': 4, 'c': 3, 'a': 1, 'b': 1, 'e': 1})
cnt.most_common(3) # [('d', 4), ('c', 3), ('a', 1)]
cnt['b'] # 1
```