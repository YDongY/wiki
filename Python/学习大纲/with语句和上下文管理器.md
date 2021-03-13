---
title: 22. with 语句和上下文管理器
description: 
published: true
date: 2021-03-13T05:27:44.850Z
tags: 
editor: markdown
dateCreated: 2021-03-13T05:27:18.427Z
---

# with 语句的使用

下面是向文件中写入数据的一般步骤示例代码:

```python
# 1、以写的方式打开文件
f = open("1.txt", "w")

# 2、写入文件内容
f.write("hello world")

# 3、关闭文件
f.close()
```

但是有时由于文件读写时都有可能产生 IOError ，一旦出错，后面的 f.close() 就不会调用，又因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的。为了保证无论是否出错都能正确地关闭文件，我们可以使用 `try ... finally` 来解决

```python
try:
    # 1、以读的方式打开文件
    f = open("1.txt", "r")
    # 2、读取文件内容
    f.write("xxxxx")
except IOError as e:
    print("文件操作出错", e)
finally:
    # 3、关闭文件
    f.close()
```

这种方法虽然代码运行良好，但是缺点就是代码过于冗长,并且需要添加 try-except-finally 语句,不是很方便，也容易忘记。

在这种情况下，Python 提供了 with 语句的这种写法，既简单又安全，并且 **with 语句执行完成以后自动调用关闭文件操作，即使出现异常也会自动调用关闭文件操作。**

```python
# 1、以写的方式打开文件
with open("1.txt", "w") as f:
    # 2、读取文件内容
    f.write("hello world")
```

# 上下文管理器

with 语句之所以这么强大，背后是由上下文管理器做支撑的，也就是说刚才使用 open 函数创建的文件对象就是就是一个上下文管理器对象。

而一个类只要实现了`__enter__()`和`__exit__()`这个两个方法，通过该类创建的对象我们就称之为上下文管理器

定义一个 File 类，实现 `__enter__()` 和 `__exit__()`方法，然后使用 with 语句来完成操作文件，示例代码：

```python
class File(object):

    # 初始化方法
    def __init__(self, file_name, file_model):
        # 定义变量保存文件名和打开模式
        self.file_name = file_name
        self.file_model = file_model

    # 上文方法
    def __enter__(self):
        print("进入上文方法")
        # 返回文件资源
        self.file = open(self.file_name,self.file_model)
        return self.file

    # 下文方法
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("进入下文方法")
        self.file.close()

if __name__ == '__main__':

    # 使用with管理文件
    with File("1.txt", "r") as file:
        file_data = file.read()
        print(file_data)
```

- `__enter__` 表示上文方法，需要返回一个操作文件对象
- `__exit__` 表示下文方法，with 语句执行完成会自动执行，即使出现异常也会执行该方法。

> 注意：其中 `__exit__()` 方法的返回值，如果为 True，则会把捕获到的异常向上冒泡，传入到调用方。如果为 False 或者没有返回值，不会把捕获到的异常向上冒泡。
{.is-warning}

# 利用 contextmanager 实现上下文管理器

Python 还提供了一个 @contextmanager 的装饰器，更进一步简化了上下文管理器的实现方式。通过 yield 将函数分割成两部分，yield 上面的语句在 `__enter__` 方法中执行，yield 下面的语句在 `__exit__` 方法中执行，紧跟在 yield 后面的参数是函数的返回值

```python
# 导入装饰器
from contextlib import contextmanager


# 装饰器装饰函数，让其称为一个上下文管理器对象
@contextmanager
def my_open(path, mode):
    try:
        # 打开文件
        file = open(file_name, file_mode)
        # yield之前的代码好比是上文方法
        yield file
    except Exception as e:
        print(e)
    finally:
        print("over")
        # yield下面的代码好比是下文方法
        file.close()
        
# 使用with语句
with my_open('out.txt', 'w') as f:
    f.write("hello , the simplest context manager")
```

# 兼容 with 语句创建一个网络连接

```python
from socket import socket, AF_INET, SOCK_STREAM


class LazyConnection:

    def __init__(self, address, family=AF_INET, type=SOCK_STREAM):
        self.address = address
        self.family = family
        self.type = type
        self.sock = None

    def __enter__(self):
        if self.sock is not None:
            raise RuntimeError('Already connected')
        self.sock = socket(self.family, self.type)
        self.sock.connect(self.address)
        return self.sock

    def __exit__(self, exc_ty, exc_val, tb):
        self.sock.close()
        self.sock = None


from functools import partial

conn = LazyConnection(('www.python.org', 80))
# Connection closed
with conn as s:
    # conn.__enter__() executes: connection open
    s.send(b'GET /index.html HTTP/1.0\r\n')
    s.send(b'Host: www.python.org\r\n')
    s.send(b'\r\n')
    print(s.recv(8192))
    resp = b''.join(iter(partial(s.recv, 8192), b''))
    # conn.__exit__() executes: connection closed
```








