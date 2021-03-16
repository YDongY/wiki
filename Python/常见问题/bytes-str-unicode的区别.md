---
title: bytes、str 与 unicode 的区别
description: 
published: true
date: 2021-03-16T05:53:55.076Z
tags: 
editor: markdown
dateCreated: 2021-03-16T05:53:55.076Z
---

Python 3 有两种表示字符序列的类型：

- bytes：包含 8 位 二进制
- str：包含 Unicode 字符

Python 2 也有两种字符序列的类型：

- str：包含 8 位 二进制
- unicode：包含 Unicode 字符

要把 Unicode 字符表示为二进制数据最常见的编码方式就是 `UTF-8`。通过：

- encode 方法：Unicode 字符转换为二进制数据
- decode 方法：二进制数据转化为 Unicode

下面代码是在 Python3 中编写接受 str 或 bytes ，并总是返回 str 的方法：

```python
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode('utf-8')  # bytes => unicode(str)
    else:
        value = bytes_or_str

    return value


def to_bytes(bytes_or_str):
    if isinstance(bytes_or_str, str):
        value = bytes_or_str.encode('utf-8')  # unicode(str) => bytes
    else:
        value = bytes_or_str
    return value


if __name__ == '__main__':
    print(to_bytes("中国"))  # b'\xe4\xb8\xad\xe5\x9b\xbd'
    print(to_str(b'\xe4\xb8\xad\xe5\x9b\xbd'))  # 中国
```