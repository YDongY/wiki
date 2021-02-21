---
title: 03-关键字和sizeof
description: 
published: true
date: 2021-02-21T13:34:46.971Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:46.671Z
---

# 关键字和 sizeof

{{< details title="要点回顾：" open=true >}}

1. 给变量或者常量起名称时候，不要使用关键字，否则会产生歧义
2. sizeof 不是函数，用于计算一个数据类型大小，单位字节

{{< /details >}}

## 关键字

| 数据类型关键字 | 控制语句关键字 | 存储类关键 | 其他关键字 |
| :------------: | :------------: | :--------: | :--------: |
|     `char`     |      `if`      |   `auto`   |  `sizeof`  |
|    `short`     |     `else`     |  `extern`  | `typedef`  |
|     `int`      |    `switch`    | `register` | `volatile` |
|     `long`     |     `for`      |  `static`  |            |
|    `float`     |     `case`     |  `const`   |            |
|    `double`    |   `default`    |            |            |
|   `unsigned`   |      `do`      |            |            |
|    `struct`    |    `break`     |            |            |
|    `union`     |   `continue`   |            |            |
|     `enum`     |     `goto`     |            |            |
|     `void`     |    `return`    |            |            |

## sizeof

- sizeof 不是函数，所以不需要包含任何头文件，它的功能是计算一个数据类型的大小，单位为字节
- sizeof 的返回值为 size_t
- size_t 类型在 32 位操作系统下是 unsigned int，是一个无符号的整数

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{

    int i1 = 10;
    int i2 = 20;
    int size = sizeof(i1);
    printf("%d \n", size); // 4
    printf("%d \n", sizeof i1); // 4，不推荐
    printf("%d \n", sizeof(int)); // 4

    // 查看内存地址
    printf("%p \n",&i1); // 0x7fffb3da3468
    printf("%p \n",&i2); // 0x7fffb3da3464

    printf("sizeof(char) = %u \n", sizeof(char)); // sizeof(char) = 1
    int a;
    printf("sizeof(a) = %u \n", sizeof(a)); // sizeof(a) = 4
    return 0;
}
```
