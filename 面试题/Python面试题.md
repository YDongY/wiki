---
title: Python 面试题
description: 人生苦短，我用 Python
published: true
date: 2021-03-06T02:41:17.013Z
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


# 输入输出

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
 

# 条件循环

# 异常

## 说明异常模块中关键字的相关含义

- try ：try 子句包含试图产生异常的代码
- execpt：根据异常类型捕获 try 子句中产生异常
- else ：当 try 子句中没有任何异常出现时，执行 else 子句中的代码
- finally ：收尾工作，无论是否产生异常都会执行 finally 子句中的代码。

> https://docs.python.org/zh-cn/3/tutorial/errors.html#handling-exceptions
{.is-success}

# 文件操作

## 简述 with 方法打开处理文件帮我们做了什么

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

```python
dic = {"a": 2, "c": 3, "b": 1}

print(sorted(dic.items(), key=lambda item: item[0])) # 按键
# [('a', 2), ('b', 1), ('c', 3)]

print(sorted(dic.items(), key=lambda item: item[1])) # 按值
# [('b', 1), ('a', 2), ('c', 3)]
```

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

# 函数

## `*agrs`和 `**kwargs` 是什么意思

# 面向对象

# 内存管理与垃圾回收

# 系统编程

# 网络编程
   