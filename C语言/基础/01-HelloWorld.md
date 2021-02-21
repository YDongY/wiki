---
title: 01-HelloWorld
description: 
published: true
date: 2021-02-21T13:34:41.693Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:39.371Z
---

# Hello World

{{< details title="要点回顾：" open=true >}}

- 编译过程
- 分步编译
- 反编译

{{< /details >}}

使用 C 语言实现 Hello World 程序：

```c
#include <stdio.h>

// 有且只有一个 main 函数，程序运行入口
int main()
{
    printf("hello world\n");
    return 0;
}
```

## GCC 编译

1. 预处理：宏定义展开、头文件展开、条件编译、去除注释，不做语法检查
2. 编译：检查语法，将预处理后文件编译生成汇编文件
3. 汇编：将汇编文件生成目标文件（二进制文件）
4. 链接：链接依赖库生成最终可执行程序（`ldd xxx`可以查看动态库）

```bash
$ ldd a.out
linux-vdso.so.1 (0x00007ffc76193000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007efc786e1000)
/lib64/ld-linux-x86-64.so.2 (0x00007efc788c3000)
```

### 分步编译

- 预处理：`gcc -E hello.c -o hello.i`
- 编译：`gcc -S hello.i -o hello.s`
- 汇编目标代码：`gcc -c hello.s -o hello.o`
- 链接：`gcc hello.o -o hello`

### 一步编译

```bash
$ gcc hello.c -o hello
```

### 反汇编

```bash
$ gcc -S hello.c -o hello.s
```
