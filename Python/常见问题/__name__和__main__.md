---
title: __name == "__main__ " 是什么意思
description: 
published: true
date: 2021-03-14T05:56:54.844Z
tags: 
editor: markdown
dateCreated: 2021-03-14T05:54:20.712Z
---

假设存在下面两个文件：

- `a.py`

```python
def f():
    print(f"{__name__}")

if __name__ == "__main__":
    f()
```

- `b.py`

```python
import a

a.f()
```

当把`a.py` 当做脚本使用时，`f()`函数输出`__main__`。当把`a.py`当做模块导入到`b.py`中执行，此时`f()`函数输出`a`。

也就说`a.py`当做脚本时运行，`__name__`就等于`__main__`。当`a.py`作为模块运行时，`__name__`就等于文件名`a`

而在文件中写入`if __name__ == "__main__"`表示，仅当文件作为脚本运行条件成立。