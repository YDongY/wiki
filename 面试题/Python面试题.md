---
title: Python 面试题
description: 人生苦短，我用 Python
published: true
date: 2021-03-06T05:28:14.003Z
tags: python, 面试题
editor: markdown
dateCreated: 2021-03-04T09:14:14.448Z
---

# Python 特性

## 列出 5 个标准库

| 模块名称              | 描述         |
| --------------------- | ------------ |
| `os`                  | 操作系统接口 |
| `glob`                | 文件通配符   |
| `argparse`            | 命令行参数   |
| `math`                | 数学         |
| `urllib`、`smtplib`   | 互联网访问   |
| `datetime`            | 日期时间     |
| `zlib、gzip、tarfile` | 数据压缩     |
| `timeit`              | 性能测试     |
| `threading`           | 多线程       |
| `logging`             | 日志         |

> https://docs.python.org/zh-cn/3/library/index.html
> https://docs.python.org/zh-cn/3/tutorial/stdlib.html#brief-tour-of-the-standard-library
{.is-success}

## Python 的内建数据类型有哪些

- 数字类型：int、float、complex
- 序列类型：list、tuple、range
- 映射类型：dict
   
> https://docs.python.org/zh-cn/3/library/stdtypes.html
{.is-success}

## Python 2 和 3 的区别，列举 5 个

| 不同 | Python2                            | Python3                         |
| ---- | ---------------------------------- | ------------------------------- |
| 打印 | `print "hello world"`              | `print("hello world")`          |
| 除法 | `10 / 3 = 3`                       | `10 / 3 = 3.333333333`          |
| 输入 | `raw_input()`                      | `input()`                       |
| 不等 | `!=`、`<>`                         | `!=`                            |
| 编码 | `unicode:字符串`<br>`str:字节序列` | `str:字符串`<br>`byte:字节序列` |

## Python 2 和 3 的 range(100) 有什么区别

```python
# py2：返回一个 list 对象
>>> range(10)
[0, 1, 2, 3, 4, 5, 6, 7, 8, 

# py3：返回一个 range 对象
>>> range(10)
range(0, 10)
```

## pass 的作用

pass 是一个空操作 --- 当它被执行时，什么都不发生。它适合当语法上需要一条语句但并不需要执行任何代码时用来临时占位
 
```python
def f(arg):
    pass
class C:
    pass
```

> https://docs.python.org/zh-cn/3/reference/simple_stmts.html#the-pass-statement
{.is-success}

## 列举布尔值为 False 的值

一个对象在默认情况下均被视为真值，除非当该对象被调用时其所属类定义了 `__bool__()` 方法且返回 False 或是定义了 `__len__()` 方法且返回零。下面基本完整地列出了会被视为假值的内置对象:

- 假值的常量: `None` 和 `False`
- 任何数值类型的零: `0, 0.0, 0j, Decimal(0), Fraction(0, 1)`
- 空的序列和多项集: `'', (), [], {}, set(), range(0)`

> https://docs.python.org/zh-cn/3/library/stdtypes.html#truth-value-testing
{.is-success}

## 单引号、双引号、三引号的用法

三种引号都是表示字符串，用法基本相同，主要是防止配合使用造成的转义问题。

- 单引号: `'允许包含有 "双" 引号'`
- 双引号: `"允许包含有 '单' 引号"`
- 三重引号: `'''三重单引号''', """三重双引号"""`

> https://docs.python.org/zh-cn/3/library/stdtypes.html#text-sequence-type-str
{.is-success}

## 列举三条 PEP8 的编码规范

- 缩进，用 4 个空格，不要用制表符
- 换行，一行不超过 79 个字符
- 运算符前后、逗号后要用空格，但不要直接在括号内使用： `a = f(1, 2) + g(3, 4)`
- 类和函数的命名要一致，命名类用 `UpperCamelCase`，命名函数与方法用 `lowercase_with_underscores`。命名方法中第一个参数总是用 `self` 


# 基本语法

## Python 中断言方法举例

```python
>>> a = 1
>>> assert a > 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AssertionError
```

## Python 中生成随机整数、随机小数、0~1之间小数的方法

- 随机整数：`random.randint(a, b)`返回随机整数 N 满足 a <= N <= b

```python
>>> import random
>>> random.randint(1,100)
26
>>> random.randint(1,100)
47
```

- 0~1：`random.random()`返回 `[0.0, 1.0)` 范围内的下一个随机浮点数。

```python
>>> random.random()
0.5384030446025826
>>> random.random()
0.9920142833314871
```

- 随机小数：`random.uniform(a, b)`返回一个随机浮点数 N ，当 a <= b 时 a <= N <= b ，当 b < a 时 b <= N <= a 。

```python
>>> random.uniform(1, 100)
17.341271519550205
>>> random.uniform(1, 100)
8.95157964898575
```

> https://docs.python.org/zh-cn/3/library/random.html#module-random
{.is-success}

## 用 lambda 表达式实现两个数相乘

```python
>>> mul = lambda x:x*x
>>> mul(10)
100
```

## Python 中交换两个值

```python
a = 10
b = 20
```

```python
# 1.直接交换
a, b = b, a

# 2.加法
a = a + b
b = a - b
a = a -b

# 3.异或
a = a ^ b
b = a ^ b
a = a ^ b
```

## 下面代码输出结果是多少

```python
[1,2,3] + [4,5,6]
```

```python
[1, 2, 3] + [4, 5, 6] = [1, 2, 3, 4, 5, 6]
```

## 简述 all() 和 any() 方法

- `any()`：方法接受一个 iterable，如果任一项为真值则返回 True。 如果 iterable 为空，返回 False。等价于：

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```

- `all()`：方法接受 一个 iterable，如果 iterable 的所有元素均为真值（或可迭代对象为空）则返回 True 。 等价于:

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

> https://docs.python.org/zh-cn/3/library/functions.html?highlight=any#any
> https://docs.python.org/zh-cn/3/library/functions.html?highlight=any#all
{.is-success}

## 举例说明 zip() 函数的用法

```python
a = [1, 2, 3]
b = [4, 5, 6]

print(list(zip(a, b)))  # [(1, 4), (2, 5), (3, 6)]
print(set(zip(a, b)))  # {(2, 5), (1, 4), (3, 6)}
print(dict(zip(a, b)))  # {1: 4, 2: 5, 3: 6}

a = [1, 2, 3]
b = [4, 5]

print(list(zip(a, b)))  # [(1, 4), (2, 5)]
print(set(zip(a, b)))  # {(2, 5), (1, 4)}
print(dict(zip(a, b)))  # {1: 4, 2: 5}

a = [1, 2, 3]
b = [4, 5]
c = [7, 8]

print(list(zip(a, b, c)))  # [(1, 4, 7), (2, 5, 8)]
print(set(zip(a, b, c)))  # {(2, 5, 8), (1, 4, 7)}
print(dict(zip(a, b, c)))  # error
```

> https://docs.python.org/zh-cn/3/library/functions.html#zip
{.is-success}

## Python 实现一个斐波那契数列

```python
def fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a+b
    return a
    
def fib2(n):
    if n <= 1:
        return n
    return fib2(n-1) + fib2(n-2)

fib3 = lambda n: n if n <= 1 else fib3(n-1)+fib3(n-2)
print(fib3(10))
```

## 用 Python 实现一个二分查找的函数。

```python

```

## Python 递归的最大层数？如何查看

```python
>>> import sys
>>> sys.getrecursionlimit()
1000
```

> https://docs.python.org/zh-cn/3/library/sys.html?highlight=sys%20setrecursionlimit#sys.setrecursionlimit
> https://docs.python.org/zh-cn/3/library/sys.html?highlight=sys%20setrecursionlimit#sys.getrecursionlimit
{.is-success}

## is 和 == 的区别

is 比较变量地址，即变量是否引用同一个对象，== 比较的是变量所引用对象的值是否相同

```python
>>> a = [1, 2, 3]
>>> b = [1, 2, 3]
>>> a == b
True
>>> a is b
False
>>> id(a)
140451796371968
>>> id(b)
140451796370240
```

## `if __name__ = __main__ `的作用

[https://ydongy.cn/zh/Python/%E5%AF%BC%E5%85%A5%E6%A8%A1%E5%9D%97#__name__-__main__%E4%BD%9C%E7%94%A8](https://ydongy.cn/zh/Python/%E5%AF%BC%E5%85%A5%E6%A8%A1%E5%9D%97#__name__-__main__%E4%BD%9C%E7%94%A8)

# 异常

## 说明异常模块中关键字的相关含义

- try ：try 子句包含试图产生异常的代码
- execpt：根据异常类型捕获 try 子句中产生异常
- else ：当 try 子句中没有任何异常出现时，执行 else 子句中的代码
- finally ：收尾工作，无论是否产生异常都会执行 finally 子句中的代码。

> https://docs.python.org/zh-cn/3/tutorial/errors.html#handling-exceptions
{.is-success}

## 自定义异常

```python
class InputError(Exception):
    '''当输出有误时，抛出此异常'''
    #自定义异常类型的初始化
    def __init__(self, value):
        self.value = value
    # 返回异常类对象的说明信息
    def __str__(self):
        return ("{} is invalid input".format(repr(self.value)))
   
try:
    raise InputError(1) # 抛出 MyInputError 这个异常
except InputError as err:
    print('error: {}'.format(err))
```

> https://docs.python.org/zh-cn/3/tutorial/errors.html#user-defined-exceptions
{.is-success}

# 文件操作

## 简述 with 方法打开处理文件帮我们做了什么

- 传统方式对文件进行操作，首先打开文件，然后进行处理，最后关闭文件资源，处理期间如果遇到任何异常情况，最终也应该关闭文件资源。代码实现如下：

```python
try:
    f = open("file.txt")
except:
    pass
finally:
    f.close()
```

- 当使用 with 上下文管理器打开文件时，其内部的 `__enter__` 方法和 `__exit__` 方法会处理打开文件和关闭文件

```python
with open("file.txt") as f:
    pass
```


## Python 如何删除一个文件

```python
os.remove(path)
````

> https://docs.python.org/zh-cn/3/faq/library.html#how-do-i-delete-a-file-and-other-file-questions
> https://docs.python.org/zh-cn/3/library/os.html#os.remove
{.is-success}

## Python 如何 copy 一个文件

```python
shutil.copyfile(src, dst, *, follow_symlinks=True)

import shutil
shutil.copyfile('./file.txt','./file.txt.bak')
```

> https://docs.python.org/zh-cn/3/faq/library.html#how-do-i-copy-a-file
> https://docs.python.org/zh-cn/3/faq/library.html#how-do-i-copy-a-file
{.is-success}


# 模块与包

# 数据类型

## 什么是可变数据类型和不可变数据类型，并阐述原理

- 不可变数据类型：数值、字符串、元组

不可变类型指的是对象所在内存块里面的值不可以改变，如果改变了变量的值相当于新建了一个对象，变量引用新对象。而对于多个变量具有相同值，其只是增加引用计数，本质上在内存中只有一个对象，即这些变量引用都指向同一个地址

```python
a = 1
print(id(a)) # 9784896
b = 1
print(id(b)) # 9784896
```

- 可变类型：列表、字典

不可变类型指对象的值可以发生改变，通过对变量进行 `append`、`+=`等操作后，只改变了变量的值，而不会新建一个对象，即变量引用对象的地址也不会变化。但是当不同变量引用相同内容的时，会新创建一个对象

```python
a = [1,2]
print(id(a)) # 140451796862144
b = [1,2]
print(id(b)) # 140451796696512
```

## 字典根据键或者值从小到大排序

- 使用 sorted 函数

```python
dic = {"a": 2, "c": 3, "b": 1}

print(sorted(dic.items(), key=lambda item: item[0])) # 按键
# [('a', 2), ('b', 1), ('c', 3)]

print(sorted(dic.items(), key=lambda item: item[1])) # 按值
# [('b', 1), ('a', 2), ('c', 3)]
```

- 使用 zip 函数

```python
dic = {"a": 2, "c": 3, "b": 1}

print(sorted(zip(dic.keys(),dic.values())))
# [('a', 2), ('b', 1), ('c', 3)]

print(sorted(zip(dic.values(),dic.keys())))
# [(1, 'b'), (2, 'a'), (3, 'c')]
```

## 如何反向迭代序列

```python
li = [1, 2, 3, 4]

for i in reversed(li):
    print(i, end=" ")
    
for i in sorted(li, reverse=True):
    print(i, end=" ")
```

> https://docs.python.org/zh-cn/3/library/functions.html#reversed
{.is-success}


## 判断以下是什么类型

- `a = (1,)`：元组
- `b = (1)`：int
- `c = ("1")`：str

## 一行代码展开该列表

```python
输入：[[1,2],[3,4],[5,6]] 
输出：[1,2,3,4,5,6]
```

```python
lists = [[1, 2], [3, 4], [5, 6]]

print([item for inner in lists for item in inner])
```

## 请反转字符串 `aStr`

```python
>>> print('aStr'[::-1])
rtSa
```

## 请按照 list1 中元素的 age 由大到小排序

```python
list1 = [{"name":'a',"age":20},{"name":'b',"age":30},{"name":'c',"age":25}]
```

```python
print(sorted(list1, key=lambda item: item["age"]))
```

# 函数

## `*agrs`和 `**kwargs` 是什么意思

## 一句话解释什么样的语言能够用装饰器

- 函数可以作为参数传递的语言，可以使用装饰器

## map 函数和 reduce 函数

- `map(function, iterable, ...)`：返回一个将 function 应用于 iterable 中每一项并输出其结果的迭代器

```python
def func(n):
    return n*2
    
print(map(func, [1, 2, 3])) # <map object at 0x7fe03a02f3a0>
print(list(map(func, [1, 2, 3]))) # [2, 4, 6]

res = map(lambda n: n*2, [1, 2, 3])
```

- `functools.reduce(function, iterable[, initializer])`：将两个参数的 function 从左至右积累地应用到 iterable 的条目，以便将该可迭代对象缩减为单一的值

```python
from functools import reduce
res = reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])
print(res)  # 15

# 相当于计算 ((((1+2)+3)+4)+5) 的值
```

> https://docs.python.org/zh-cn/3/library/functions.html#map
> https://docs.python.org/zh-cn/3/library/functools.html?highlight=reduce#functools.reduce
{.is-success}


# 面向对象

# 内存管理与垃圾回收
# 正则表达式

## `(.*)` 和 `(.*?)`匹配区别

- `.`：在默认模式，匹配除了换行的任意字符
- `*`：对它前面的正则式匹配0到任意次重复，尽量多的匹配字符串。`ab*` 会匹配 `'a'`、`'ab'`或者 `'a'` 后面跟随任意个 `'b'`。
- `?`：对它前面的正则式匹配0到1次重复。 `ab?` 会匹配 `'a'` 或者 `'ab'`。

- `(.*)`是贪婪匹配，会把满足正则的尽可能多的往后匹配
- `(.*?)`是非贪婪匹配，会把满足正则的尽可能少匹配
 
```python
import re
s = "<a> b <c>"
res1 = re.findall("<(.*)>", s)
print(res1)  # ['a> b <c']
res2 = re.findall("<(.*?)>", s)
print(res2)  # ['a','b']
```

> https://docs.python.org/zh-cn/3/library/re.html#regular-expression-syntax
{.is-info}


## Python 正则中 search()  和 match() 区别

- `match()` 函数只检测 re 是不是在 string 的开始位置匹配
- `search()`会扫描整个 string 查找匹配

也就是说 match() 只有在 0 位置匹配成功的话才有返回，如果不是开始位置匹配成功的话，match() 就返回 none

# 系统编程

# 网络编程
   