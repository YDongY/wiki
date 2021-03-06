---
title: 08-typedef
description: 
published: true
date: 2021-03-31T07:46:25.460Z
tags: c
editor: markdown
dateCreated: 2021-02-21T14:39:24.844Z
---

# typedef

typedef 为 C 语言的关键字，作用是为一种数据类型(基本类型或自定义数据类型)定义一个新名字，不能创建新类型。

```c
typedef int MYINT; // 起别名

int main(int argc, char const *argv[]) {
    MYINT a = 10;
    printf("%d \n", a);
}
```

## 简化结构体

```c
struct Student {
    int age;
    int id;
};
typedef struct Student stu1; // 起别名

// 简化
typedef struct Student2 {
    int age;
    int id;
} stu2;

int main(int argc, char const *argv[]) {
    struct Student s1 = {20, 1};

    stu1 s2 = {20, 1};
    stu2 s3 = {20, 1};

    func();
}
```

## 区分数据类型

```c
void func() {
    // typedef 区分数据类型

    char *p1, p11;
    printf("p1=%lu ,p11=%lu", sizeof(p1), sizeof(p11)); // p1=8 ,p11=1

    // p1 是 char *
    // p11 是 char

    typedef char *PCHAR; // 区分数据类型

    PCHAR p2, p22;

    // p2 是 char *
    // p22 是 char *

    printf("p2=%lu ,p22=%lu", sizeof(p2), sizeof(p22)); // p2=8 ,p22=8
}
```

# define 与 typedef 区别

- 与 `#define` 不同，`typedef` 仅限于数据类型，而不是能是表达式或具体的值
- define 发生在预处理，typedef 发生在编译阶段

```c
#define IP int *
IP p,q -> int *p,q;

typedef int *IP;
IP p,q -> int *p,*q;
```
