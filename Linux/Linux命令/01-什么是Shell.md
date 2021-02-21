---
title: 01-什么是Shell
description: 
published: true
date: 2021-02-21T13:35:28.003Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:55:49.392Z
---

# Linux Shell 和 Shell 脚本

当我们使用 Linux 操作系统时，实际上间接与 **Shell** 交互。 那么 Shell 、Linux 操作系统、Shell 脚本三者关系又是啥🤔？解密之前，先来看看下面三个术语是否了解：

- Kernel
- Shell
- Terminal

## 什么是 Kernel？

Kernel 翻译过来就是内核。内核是一个计算机程序，它是计算机操作系统的核心，完全控制系统中的所有内容，可以直接与计算机硬件资源打交道。其主要用途是管理 Linux 系统的以下资源：

- 文件系统管理
- 软件程序管理
- I/O 管理
- 系统内存管理
- 硬件设备管理

所以有一个容易忽视的误区：[**Linus Torvalds** ](https://en.wikipedia.org/wiki/Linus_Torvalds)已经开发了Linux OS， 实际是错误的，他只负责 Linux 内核的开发。

**完整的 Linux 系统 = Kernel + GNU 系统工具链和库 + 其他管理脚本 + 安装脚本。**

> 什么是 GNU?

## 什么是 Shell？

Shell 是特殊的用户程序，为用户提供使用操作系统服务的接口。Shell 接受用户输入的命令，并将其转换为内核可以理解的命令。所以它是一种命令语言解释器，执行从键盘或文件之类的输入设备读取的命令。当用户登录或启动终端时，shell 将启动。

![Linux Shell](/docs/Shell/LinuxCommandLine/01-linux-shell.png)

Shell 大致分为两类：

- **命令行 Shell**

其实真正的命令行 Shell 是**文本命令行界面**（command line interface ，CLI）。`CLI` 只能接受文本输入，也只能显示出文本和基本的图形输出。目前大多数 Linux 发行版都是图形化桌面。想要进入 CLI 就是让 Linux 系统退出图形化桌面模式，进入文本模式。这种模式称作 **Linux 控制台**。

而我们经常使用的是 `Terminal`，常用的有 `GNOME Terminal、xterm`等等。它们都属于终端仿真器，即在一个桌面图形化窗口中模拟控制台终端的使用。

- 图形化 Shell

图形化 Shell 程序提供了基于图形用户界面（GUI）操作程序的方法，用户无需为每个操作键入命令。例如：双击打开文件或文件夹、移动和调整窗口大小等。

有几种常用的 Linux 系统的 shell，例如：

- `sh`：Unix 默认 shell

- `bash`：是 Brian Fox 为 GNU 项目的编写出了一种全新的 shell：Bourne Again Shell（ bash），bash shell 与 sh 完全兼容，同时又增添了一些来自 csh、 ksh 等的功能。已经称为大多数 Linux 默认 shell。
- `csh`： C Shell 的语法和用法与 C 编程语言非常相似。
- `dash`：Debian Linux 发行版创建并改进，提供了一些高级命令行编辑能力。但并不是 Debian Linux发行版中并默认的shell，而只将 dash shell 作为安装脚本的快速启动 shell，用于安装发行版文件。但是 Ubuntu 发行版例外。Ubuntu 将 bash shell 用作默认的交互 shell，但将 dash shell 用作默认的 `/bin/sh`。而大多数 Linux 发行版 `/bin/sh` 文件是链接到 shell 程序 `/bin/bash` 的一个符号链接，其目的是能够很好的移植 Unix 环境的 shell 脚本，因为在 Unix 世界中，默认 shell 一直是
  `/bin/sh`，`/bin/bash` 能够很好的兼容。但是 Ubuntu 却把 `/bin/sh` 链接到 `/bin/dash` ，从而可能导致某些命令无法使用。
- `zsh`：由 `Paul Falstad` 开发的一个开源 Unix shell，提供了更多高级功能。

{{< hint info >}}
**shell 简史**：第一个流行的 shell 是由 Steven Bourne 发展出来，为此纪念称为 `Bourne shell` 简称 `sh`，另一个流行的 shell 是由柏克莱大学的 Bill Joy 设计依附于 BSD 版的 Unix 系统中的 shell，由于语法类似 C 语言，由此得名 `C shell` 简称 `csh`。
{{< /hint>}}

## 什么是 Shell 脚本？

由于 shell 可以将命令作为文件的输入，所以每当我们想要执行一堆命令时，就可以把要执行的命令写入文本，以避免重复的工作。这些文件称为 **Shell 脚本**或 **Shell 程序**。类似于 MS-DOS 中的批处理文件，我们知道批处理文件一般以 `.bat` 结尾，而 shell 脚本均以 `.sh` 结尾作为文件扩展名。

编写 shell 脚本目的：

- 为了避免重复的工作和自动化
- 系统管理员使用Shell脚本进行常规备份
- 系统监控

shell 脚本的优缺点：

- 优点
  - 命令和语法与直接在命令行中输入的命令和语法完全相同，因此程序员无需切换到完全不同的语法
  - 编写 Shell 脚本要快得多
- 缺点：
  - 执行速度慢，部分命令属于外部命令，实际就是调用相应的应用程序
  - 不太适合大型和复杂的任务

# Bash Shell

BASH 是 Linux 系统中使用最广泛的 shell。在 Linux 系统和 macOS 中，它用作默认的登录 Shell。也可以将其安装在 Windows OS上。通常在用户登录终端时启动。在 `/etc/passwd` 文件中可以对用户进行配置。

```bash
$ cat /etc/passwd
ydongy : x : 1000 : 1000 : : /home/ydongy : /usr/bin/zsh
```

最后一个字段指定了用户使用的 shell 程序。

## bash 功能

- **历史记录（history）**

默认会记录前一登录的所执行过的 1000 个命令，保存在 `~/.bash_history` 文件中，而至于本次登录所执行的命令都被暂存在内存中，当注销系统后，才会被记录到 `~/.bash_history`

- **补全功能（tab）**
- **命令别名（alias）**
- **通配符**

## bash 手册

Linux 发行版自带 shell 命令实在太多，如果想要弄清楚各种命令参数的使用，可以通过 `man` 命令来访问 bash 手册。

```bash
$ man ls

LS(1)                                   User Commands                                  LS(1)

NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...

DESCRIPTION
       List  information  about  the FILEs (the current directory by default).  Sort entries
       alphabetically if none of -cftuvSUX nor --sort is specified.

       Mandatory arguments to long options are mandatory for short options too.

       -a, --all
              do not ignore entries starting with .

       -A, --almost-all
              do not list implied . and ..

       --author
              with -l, print the author of each file

       -b, --escape
              print C-style escapes for nongraphic characters

Manual page ls(1) line 1 (press h for help or q to quit)
```

{{< hint info >}}
如果不记得命令名，可以使用关键字搜索手册：`man -k cmd`
{{< /hint >}}

手册页将与命令相关的信息分成了不同的节，如有：`NAME`、`SYNOPSIS`、`DESCRIPTION` 等等。除此之外，手册页还有对应的内容区域，就在手册的第一行显示，每个内容区域都分配了一个数字，从 1 开始，一直到 9

| 区域号 | 所包含内容               |
| ------ | ------------------------ |
| 1      | 可执行程序或 shell 命令  |
| 2      | 系统调用                 |
| 3      | 库调用                   |
| 4      | 特殊文件                 |
| 5      | 文件格式或约定           |
| 6      | 游戏                     |
| 7      | 概览、约定及杂项         |
| 8      | 超级用户和系统管理员命令 |
| 9      | 内核例程                 |

如果只看各部分内容的简介：输入 `man 1 intro`阅读第 1 部分，输入 `man 2 intro`阅读第 2 部分等等。

# 深入理解 shell

## shell 的父子关系

当我们的终端仿真器启动是默认会启动一个 shell，也就是父 shell。如果此时你继续输入一个 `bash` 命令，你可能会感觉到终端并没有发生变化，前提是你没有使用额外的 shell ，诸如：`zsh` 来美化你的终端。实际上此时会创建一个新的 shell 程序，这个 shell程序被称为子 shell，可以通过在子 shell 执行 `ps --forest -f` 命令来查看父子关系。

```bash
$ ps --forest -f
UID          PID    PPID  C STIME TTY          TIME CMD
root       35470   35452  0 16:18 pts/0    00:00:00 -bash
root       35492   35470  0 16:19 pts/0    00:00:00  \_ ps --forest -f

$ bash

$ ps --forest -f
UID          PID    PPID  C STIME TTY          TIME CMD
root       35470   35452  0 16:18 pts/0    00:00:00 -bash
root       35493   35470  0 16:19 pts/0    00:00:00  \_ bash
root       35500   35493  0 16:19 pts/0    00:00:00      \_ ps --forest -f
```

{{< hint danger >}}
注意：在生成子 shell 进程时，只有部分父进程的环境被复制到子 shell 环境中。这会对包括变量在内的一些东西造成影响
{{< /hint >}}

## 进程列表

当我们想要依次执行一系列命令时，可以通过命令列表来实现，只需要只在命令之间加入分号  `;`即可。

```bash
$ ps ; pwd ; cd /etc ; ls
```

在上面的例子中，所有的命令依次执行，且 cd 命令会改变当前工作目录。但是这并不是进程列表，想要成为进程列表，这些命令必须包含在括号内。

```bash
$ (ps ; pwd ; cd /etc ; ls)
```

通过执行你会发现，cd 命令并没有改变工作目录，这是因为执行生成了一个子 shell 来执行对应的命令。想要知道是否生成了子 shell 可以借助 `echo $BASH_SUBSHELL`环境变量的值来查看，如果该命令返回 0，就表明没有子 shell。如果返回 1 或者其他更大的数字，就表明存在子 shell。

```bash
$ ps ; pwd ; cd /etc ; ls ; echo $BASH_SUBSHELL
$ (ps ; pwd ; cd /etc ; ls ; echo $BASH_SUBSHELL)
```

## shell 的内建命令

之前在探究 shell 的父子进程时发现，执行一个命令就会创建出一个子进程，这样的命令也被称为外部命令，外部命令程序通常位于`/bin`、 `/usr/bin`、 `/sbin`或`/usr/sbin`中。

ps 就是一个外部命令。可以使用 which 和 type 命令找到它

```bash
$ which ps
/usr/bin/ps
$ type ps
ps is hashed (/usr/bin/ps)
$ ll /usr/bin/ps
-rwxr-xr-x 1 root root 137688 Feb 27  2020 /usr/bin/ps*
```

所以 ps 命令执行时会创建出一个子进程，它需要花费时间和精力来设置新子进程的环境。所以说，外部命令多少还是有代价的。

而内建命令不需要使用子进程来执行，它们已经和 shell 编译成了一体，作为 shell 工具的组成部分存在。不需要借助外部程序文件来运行。

cd 和 exit 命令都内建于 bash shell，可通过 type 命令查看

```bash
$ type cd
cd is a shell builtin
$ type exit
exit is a shell builtin
```

由于不需要通过衍生出子进程来执行，也不需要打开程序文件，所以内建命令的执行速度要更快，效率也更高。

不过也存在某些命令多种实现，例如 echo 和 pwd 既有内建命令也有外部命令，要查看命令的不同实现，使用 type 命令的 `-a` 选项

```bash
$ type -a pwd
pwd is a shell builtin
pwd is /usr/bin/pwd
pwd is /bin/pwd
$ type -a echo
echo is a shell builtin
echo is /usr/bin/echo
echo is /bin/echo
```

# Linux 文件系统：拓展

在 Linux中路径名中不使用驱动器盘符， 而是将文件存储在单个目录结构中，这个目录被称为**虚拟目录（ virtual directory）**。虚拟目录将安装在 PC 上的所有存储设备的文件路径纳入单个目录结构中。

Linux 虚拟目录结构只包含一个称为**根（ root）**目录的基础目录。根目录下的目录和文件会按照访问它们的目录路径一一列出。

Linux 虚拟目录中比较复杂的部分是它如何协调管理各个存储设备?在 Linux PC 上安装的第一块硬盘称为**根驱动器**。根驱动器包含了虚拟目录的核心，其他目录都是从那里开始构建的。

Linux 会在根驱动器上创建一些特别的目录，称之为**挂载点（ mount point）**。挂载点是虚拟目录中用于分配额外存储设备的目录。虚拟目录会让文件和目录出现在这些挂载点目录中，然而实际上它们却存储在另外一个驱动器中。

![Linux File System](/docs/Shell/LinuxCommandLine/01-linux-filesystem.png)

图中 `Disk1` 和虚拟目录的根目录关联起来，第二块硬盘被挂载到了 `/home` 位置，用户目录都位于这个位置。

在 Linux 文件系统中，通用的目录名用于表示一些常见的功能，如下表：

| 目录     | 用途                                                        |
| -------- | ----------------------------------------------------------- |
| `/`      | 虚拟目录的根目录。通常不会在这里存储文件                    |
| `/bin`   | 二进制目录，存放许多用户级的 GNU 工具                       |
| `/boot`  | 启动目录，存放启动文件                                      |
| `/dev`   | 设备目录， Linux 在这里创建设备节点                         |
| `/etc`   | 系统配置文件目录                                            |
| `/home`  | 主目录， Linux 在这里创建用户目录                           |
| `/lib`   | 库目录，存放系统和应用程序的库文件                          |
| `/media` | 媒体目录，可移动媒体设备的常用挂载点                        |
| `/mnt`   | 挂载目录，另一个可移动媒体设备的常用挂载点                  |
| `/opt`   | 可选目录，常用于存放第三方软件包和数据文件                  |
| `/proc`  | 进程目录，存放现有硬件及当前进程的相关信息                  |
| `/root`  | root 用户的主目录                                           |
| `/sbin`  | 系统二进制目录，存放许多 GNU 管理员级工具                   |
| `/run`   | 运行目录，存放系统运作时的运行时数据                        |
| `/srv`   | 服务目录，存放本地服务的相关文件                            |
| `/sys`   | 系统目录，存放系统硬件信息的相关文件                        |
| `/tmp`   | 临时目录，可以在该目录中创建和删除临时工作文件              |
| `/usr`   | 用户二进制目录，大量用户级的 GNU 工具和数据文件都存储在这里 |
| `/var`   | 可变目录，用以存放经常变化的文件，比如日志文件              |

> 常见的目录名均基于文件系统层级标准（ filesystem hierarchy standard， FHS）， [官方主页](http://www.pathname.com/fhs)