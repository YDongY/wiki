---
title: Python 面试题
description: 人生苦短，我用 Python
published: true
date: 2021-03-05T13:33:39.772Z
tags: python, 面试题
editor: markdown
dateCreated: 2021-03-04T09:14:14.448Z
---

# 基本

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


   
   
   
   