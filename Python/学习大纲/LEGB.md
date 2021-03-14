---
title: 27. 作用域和命名空间
description: 
published: true
date: 2021-03-14T07:17:24.858Z
tags: 
editor: markdown
dateCreated: 2021-03-14T07:05:22.501Z
---

# namespace（命名空间）

namespace 是一个从名字到对象的映射

在不同时刻创建的命名空间拥有不同的生存期。包含**内置名称的命名空间**是在 Python 解释器启动时创建的，永远不会被删除

**模块的全局命名空间**在模块定义被读入时创建，通常，模块命名空间也会持续到解释器退出

一个**函数的本地命名空间**在这个函数被调用时创建，并在函数返回或抛出一个不在函数内部处理的错误时被删除。


## LEGB

- L：Local(function) 函数内的名字空间
- E-Enclosing function locals 外部嵌套函数的名字空间，闭包
- G-Global(module) 函数定义所在模块（文件）的名字空间
- B-Builtin(Python) Python 内置模块的名字空间

## 变量查找顺序

- L G

```python
x = 'Global'
def func():
    x = 'Enclosing'

    def func2():
        x = 'Local'

        print (x)
    func2()
print(x) # Global
func()   # Local
```

- E

```python
x = 'Global'
def func3():
    x = 'Enclosing'
    def func2():
        return x
    return func2

var = func3()
print(var()) # Enclosing
```

- B

```python
>>> print(dir(__builtins__))

['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException',
'BlockingIOError', 'BrokenPipeError', 'BufferError', 'BytesWarning',
'ChildProcessError', 'ConnectionAbortedError', 'ConnectionError', 
'ConnectionRefusedError', 'ConnectionResetError', 'DeprecationWarning', 
'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False', 
'FileExistsError', 'FileNotFoundError', 'FloatingPointError', 'FutureWarning', 
'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning', 
'IndentationError', 'IndexError', 'InterruptedError', 'IsADirectoryError', 
'KeyError', 'KeyboardInterrupt', 'LookupError', 'MemoryError', 
'ModuleNotFoundError', 'NameError', 'None', 'NotADirectoryError', 
'NotImplemented', 'NotImplementedError', 'OSError', 'OverflowError', 
'PendingDeprecationWarning', 'PermissionError', 'ProcessLookupError', 
'RecursionError', 'ReferenceError', 'ResourceWarning', 'RuntimeError', 
'RuntimeWarning', 'StopAsyncIteration', 'StopIteration', 'SyntaxError', 
'SyntaxWarning', 'SystemError', 'SystemExit', 'TabError', 'TimeoutError', 
'True', 'TypeError', 'UnboundLocalError', 'UnicodeDecodeError', 
'UnicodeEncodeError', 'UnicodeError', 'UnicodeTranslateError', 
'UnicodeWarning', 'UserWarning', 'ValueError', 'Warning', 'ZeroDivisionError', 
'__IPYTHON__', '__build_class__', '__debug__', '__doc__', '__import__', 
'__loader__', '__name__', '__package__', '__spec__', 'abs', 'all', 'any', 
'ascii', 'bin', 'bool', 'breakpoint', 'bytearray', 'bytes', 'callable', 'chr', 
'classmethod', 'compile', 'complex', 'copyright', 'credits', 'delattr', 
'dict', 'dir', 'display', 'divmod', 'enumerate', 'eval', 'exec', 'filter', 
'float', 'format', 'frozenset', 'get_ipython', 'getattr', 'globals', 
'hasattr', 'hash', 'help', 'hex', 'id', 'input', 'int', 'isinstance', 
'issubclass', 'iter', 'len', 'license', 'list', 'locals', 'map', 'max', 
'memoryview', 'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 
'property', 'range', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice', 
'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'vars', 
'zip']
```

# 作用域

> 作用域是一个命名空间可直接访问的 Python 程序的文本区域
{.is-info}
