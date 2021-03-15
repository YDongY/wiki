---
title: 21. property 属性
description: 
published: true
date: 2021-03-15T05:51:46.900Z
tags: 
editor: markdown
dateCreated: 2021-03-13T05:16:56.205Z
---

# property 属性

property 属性就是负责把一个方法当做属性进行使用，这样做可以简化代码使用。

定义 property 属性有两种方式：

1. 装饰器方式
2. 类属性方式

## 装饰器方式

```python
class Person(object):

    def __init__(self):
        self.__age = 0

    # 装饰器方式的 property, 把 age 方法当做属性使用, 表示当获取属性时会执行下面修饰的方法
    @property
    def age(self):
        return self.__age

    # 把 age 方法当做属性使用, 表示当设置属性时会执行下面修饰的方法
    @age.setter
    def age(self, new_age):
        if new_age >= 150:
            print("成精了")
        else:
            self.__age = new_age

# 创建person
p = Person()
print(p.age)
p.age = 100
print(p.age)
p.age = 1000
```

代码说明:

- @property 表示把方法当做属性使用，表示当获取属性时会执行下面修饰的方法
- @方法名.setter 表示把方法当做属性使用，表示当设置属性时会执行下面修饰的方法
- 装饰器方式的 property 属性修饰的方法名一定要一样。

## 类属性方式

```python
class Person(object):

    def __init__(self):
        self.__age = 0

    def get_age(self):
        """当获取age属性的时候会执行该方法"""
        return self.__age

    def set_age(self, new_age):
        """当设置age属性的时候会执行该方法"""
        if new_age >= 150:
            print("成精了")
        else:
            self.__age = new_age

    # 类属性方式的property属性
    age = property(get_age, set_age)

# 创建person
p = Person()
print(p.age)
p.age = 100
print(p.age)
p.age = 1000
```

property 的参数说明:

- 第一个参数是获取属性时要执行的方法
- 第二个参数是设置属性时要执行的方法

# property 套装

```python
class Human2(object):
    def __init__(self):
        self._gender = None

    # 将方法封装成属性
    @property
    def gender(self):
        print(self._gender)

    # 支持修改
    @gender.setter
    def gender(self, value):
        self._gender = value

    # 支持删除
    @gender.deleter
    def gender(self):
        del self._gender


h = Human2()
h.gender = 'F'
h.gender
del h.gender
```

- 另一种 property 写法

```python
gender  = property(get_, set_, del_, 'other property')
```


# property 纯 python 实现

```python
class Property(object):
    "Emulate PyProperty_Type() in Objects/descrobject.c"

    def __init__(self, fget=None, fset=None, fdel=None, doc=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel
        if doc is None and fget is not None:
            doc = fget.__doc__
            self.__doc__ = doc

    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        if self.fget is None:
            raise AttributeError("unreadable attribute")
        return self.fget(obj)

    def __set__(self, obj, value):
        if self.fset is None:
            raise AttributeError("can't set attribute")
        self.fset(obj, value)

    def __delete__(self, obj):
        if self.fdel is None:
            raise AttributeError("can't delete attribute")
        self.fdel(obj)

    def getter(self, fget):
        return type(self)(fget, self.fset, self.fdel, self.__doc__)

    def setter(self, fset):
        return type(self)(self.fget, fset, self.fdel, self.__doc__)

    def deleter(self, fdel):
        return type(self)(self.fget, self.fset, fdel, self.__doc__)
```


