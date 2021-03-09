---
title: 01-HelloWorld
description: 
published: true
date: 2021-03-09T06:54:37.658Z
tags: 
editor: markdown
dateCreated: 2021-02-21T14:38:31.735Z
---

# 第一个 C 程序

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

> 程序 `return 0` 的作用是把返回值传递给父进程。如果取消 `return 0` ，返回值是 printf 函数的返回值，但也有可能是 0。
{.is-info}


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

# 标识符、变量和常量

> 1. 变量声明，不会开辟内存空间
> 2. 变量定义，会开辟内存空间
> 3. 常量不能修改

## 标识符

- 标识符不能是关键字
- 标识符只能由字母、数字、下划线组成
- 第一个字符必须为字母或下划线，下划线“_”被看做是字母，通常用于命名较长的变量名，一般不要以下划线开头
- 标识符中字母区分大小写
- 传统用法，变量名使用小写字母，符号常量名全部使用大写字母。

## 变量

- 在程序运行过程中，其值可以改变
- 变量在使用前必须先定义，定义变量前必须有相应的数据类型
- 变量在编译时为其分配相应的内存空间
- 可以通过其名字和地址访问相应内存

```c
[存储类型] 数据类型 标识符 = 值;
          TYPE NAME = VALUE;
```

- 数据类型：基本数据类型、构造类型

- 存储类型
	- `auto`：默认，没有初始化功能。自动分配空间，自动回收空间（放在栈空间）
  - `static`：静态型。自动初始化为 0 或空值，并且值具有继承性。常用于修饰变量或函数，全局 static 作用范围仅限于当前文件。
  - `register`：寄存器类型，只能用来定义局部变量，大小有限制（32 位 OS 定义 32 位大小的数据类型），没有地址无法打印
  - `extern`（说明型）：意味着不能改变被说明的变量的值或类型

变量定义与声明：

- 声明变量不需要建立存储空间，如：`extern int a;`
- 定义变量需要建立存储空间，如：`int b =10;`

```c
#include <stdio.h>

int main()
{
	// extern 关键字只做声明，不能做任何定义
	// 声明一个变量 a，a 在这里没有建立存储空间
	extern int a;
	a = 10;	// err, 没有空间，就不可以赋值

	int b = 10;	//定义一个变量 b，b 的类型为 int，b 赋值为 10

	int c; // 既是声明，同时又是定义

	return 0;
}
```

## 常量

- 在程序运行过程中，其值不能被改变
- 常量一般出现在表达式或赋值语句中

常量类型：

- 整数常量：`1234、123456789L、100u、200U、11111111UL`
- 浮点数常量：`123.4、1e-2、0.8888f、3.1415926l`
- 字符常量：由单引号引起来的单个字符或转义字符，`'a'、'\n'`
- 字符串常量：由双引号引起来的一个或多个字符序列，`"a"、"hello"、""（包括一个尾0）、"abc\n\021\018"`

> `\018`：错误的，因为三个数字表示八进制，而八进制不能包含 8。最终解释为 `\0` 和 '8'
{.is-info}

- 标识常量：`#define PI 3.14` ，预处理之后宏名会被宏体所替换，不做语法检查。不能出现在赋值符的左侧。

例如：

```c
#define PI 3.14abc
int main() {
  int a = 10;
  print("%f", a * PI); // 使用宏定义
#define INIT_TYPE(TYPE, NAME) \
    do { \
        if (TYPE < 0) { \
            return NAME; \
        } \
    } while (0)

  INIT_TYPE(10, "10");
  INIT_TYPE(20, "20");
#undef INIT_TYPE
}
```

```shell
$ gcc -E xxx.c

int main() {
  int a = 10;
  print("%f", a * 3.14abc); // 直接替换，不检查语法
# 24 "c1.1.c"
  do { if (10 < 0) { return "10"; } } while (0); // 直接替换
  do { if (20 < 0) { return "20"; } } while (0); // 直接替换
}
```

# 关键字和 sizeof

> 1. 给变量或者常量起名称时候，不要使用关键字，否则会产生歧义
> 2. sizeof 不是函数，用于计算一个数据类型大小，单位字节

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

- sizeof 不是函数，所以不需要包含任何头文件，它的功能是计算一个数据类型的大小，单位为**字节**
- sizeof 的返回值为 size_t
- size_t 类型在 32 位操作系统下是 `unsigned int`，是一个无符号的整数

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















