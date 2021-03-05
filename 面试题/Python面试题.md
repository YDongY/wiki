---
title: Python 面试题
description: 人生苦短，我用 Python
published: true
date: 2021-03-05T14:08:59.908Z
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

# 输入输出

## Python 中生成随机整数、随机小数、0~1之间小数的方法

# 条件循环

# 异常

# 文件操作

## 简述 with 方法打开处理文件帮我们做了什么

# 模块与包

# 数据类型

# 函数

## `*agrs`和 `**kwargs` 是什么意思

# 面向对象

# 内存管理与垃圾回收

# 系统编程

# 网络编程
   