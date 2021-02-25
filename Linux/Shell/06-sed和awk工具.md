---
title: 06-sed和awk工具
description: 
published: true
date: 2021-02-21T14:40:18.058Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:40:14.830Z
---

# 正则表达式

| 元字符    | 功能                                         | 示例                                                |
| --------- | -------------------------------------------- | --------------------------------------------------- |
| `$`       | 行尾(以...结尾)                              | `grep 'g$' file.txt`                                |
| `*`       | 前导字符出现 0 次或连续多次                  | `grep g* file.txt`                                  |
| `.*`      | 任意长度字符                                 |                                                     |
| `.`       | 匹配除了换行符以外的任意单个字符             | `grep g. file.txt`                                  |
| `[]`      | 匹配括号里任意单个字符或一组单个字符         | `grep '[abc]' file.txt`：包含 a \| b \| c           |
| `[^]`     | 匹配不包含括号里任一单个字符或一组单个字符   | `grep '[^abc]' file.txt`：不包含 a \| b \| c        |
| `\(\)`    | 保存被匹配的字符                             |                                                     |
| `\<\>`    | 精确匹配                                     | `grep '\<world\>' file.txt`                         |
| `\<`      | 取单词的头                                   | `grep '\<go' file.txt`：以 go 开头的单词            |
| `\>`      | 取单词的尾                                   | `grep 'od\>' file.txt`：以 od 结尾的单词            |
| `\d`      | 匹配数字                                     | `grep -P '\d' file.txt`                             |
| `\s`      | 匹配空格、制表符、换页符                     | `grep -P '\s' file.txt`                             |
| `\w`      | 匹配字母数字下划线                           | `grep -P '\w' file.txt`                             |
| `\{n,\}`  | 匹配前导字符至少出现 n 次                    | `grep 'go\{2,\}' file.txt`                          |
| `\{n,m\}` | 匹配前导字符出现 n 次与 m 次之间             |                                                     |
| `\{n\}`   | 匹配前导字符连续出现 n 次                    | `grep 'go\{2\}' file.txt`：前导字符 o 连续出现 2 次 |
| `^$`      | 空行                                         | `grep '^$' file.txt`                                |
| `^[]`     | 匹配以括号里任意单个字符或一组单个字符开头   | `grep '^[abc]' file.txt`：包含 a \| b \| c 开头     |
| `^[^]`    | 匹配不以括号里任意单个字符或一组单个字符开头 | `grep '^[^abc]' file.txt`：不包含 a \| b \| c 开头  |
| `^`       | 行首(以...开头)                              | `grep '^g' file.txt`                                |

## 扩展正则

- `grep`使用必须加 `-E`
- `sed`使用必须加  `-r`

| 扩展元字符 | 功能                   | 示例                       |
| ---------- | ---------------------- | -------------------------- |
| `()`       | 组字符（看成整体）     | `grep -E '(a|b)' file.txt` |
| `+`        | 匹配一个或多个前导字符 | `grep -E 'g+' file.txt`    |
| `?`        | 匹配零个或一个前导字符 | `grep -E 'g?' file.txt`    |
| `{n,m}`    | 前导字符重复 n 到 m 次 |                            |
| `{n,}`     | 前导字符重复至少 n 次  |                            |
| `{n}`      | 前导字符重复 n 次      |                            |
| `|`        | 或                     | `grep -E '^a|^b' file.txt` |

## 其他正则

| 表达式      | 功能                             | 示例                            |
| ----------- | -------------------------------- | ------------------------------- |
| `[:alnum:]` | 字母与数字字符                   | `grep -E [[:alnum:]]+ file.txt` |
| `[:alpha:]` | 字母字符(包括大小写字母)         |                                 |
| `[:blank:]` | 空格与制表符                     |                                 |
| `[:digit:]` | 数字                             |                                 |
| `[:lower:]` | 小写字母                         |                                 |
| `[:punct:]` | 标点符号                         |                                 |
| `[:space:]` | 包括换行符，回车等在内的所有空白 |                                 |
| `[:upper:]` | 大写字母                         |                                 |

# `sed` 编辑器

`sed` 是一行一行读取文件内容并按照要求进行处理，把处理后的结果输出到屏幕。

处理步骤：

1. 首先 `sed` 读取文件中的一行内容，把其保存在一个**临时缓存区**中（也称为**模式空间**）
2. 然后根据需求处理临时缓冲区中的行，完成后把该行发送到屏幕上

使用方法：

- 命令行模式
- 脚本模式

语法格式：

```bash
sed [options] '处理动作' filename
```

常见选项：

| 选项 | 说明                                           |
| ---- | ---------------------------------------------- |
| `-e` | 进行多项(多次)编辑                             |
| `-f` | 指定 `sed` 脚本的文件名                        |
| `-i` | 原地编辑（修改源文件）                         |
| `-n` | 取消默认输出（默认会输出原内容和修改后的内容） |
| `-r` | 使用扩展正则表达式                             |

常用处理动作：

| 动作  | 说明                 |
| ----- | -------------------- |
| `'a'` | 在指定行之后插入内容 |
| `'c'` | 替换指定行所有内容   |
| `'d'` | 删除指定行           |
| `'i'` | 在指定行之前插入内容 |
| `'p'` | 打印                 |
| `'s'` | 替换                 |
| `'='` | 用来打印被匹配的行号 |

## 增删改查

语法：`sed '定位+动作' file.txt`

### 添加数据

- 指定行号前后添加数据

```bash
$ sed '3ahello' sed.txt  # 第三行后面添加 hello ，不修改源文件
$ sed -i '3ahello' sed.txt # 修改源文件

$ sed '3ihello' sed.txt  # 在第三行前面添加 hello 
```

- 指定内容前后添加数据

```bash
$ sed '/aaa/ihello' sed.txt  # 在 aaa 前面添加 hello
$ sed '/aaa/ahello' sed.txt  # 在 aaa 后面添加 hello
```

- 在最后一行添加数据

```bash
$ sed '$ahello' sed.txt  # 在最后一行添加 hello
```

### 删除数据

- 删除指定行或范围

```bash
$ sed '2d' sed.txt  # 删除第二行
$ sed '1~2d' sed.txt  # 从第一行开始，每隔两行删一次
$ sed '1,3d' sed.txt  # 删除 1-3 行
$ sed '1,3!d' sed.txt  # 取反,相当于保留 1-3 行
$ sed '$d' sed.txt  # 删除最后一行
```

- 根据内容删除指定行

```bash
$ sed '/aaa/d' sed.txt  # 删除所有含有 aaa 的行
$ sed '/aaa/,$d' sed.txt  # 删除含有 aaa 的行到最后
$ sed '/aaa/,+1d' sed.txt  # 删除含有 aaa 的行以及后面一行
$ sed '/aaa\|bbb/!d' sed.txt  # 删除不包含 aaa 或者 bbb 的行
```

### 修改数据

- 行修改：`c`

```bash
$ sed '1chello' sed.txt  # 修改第一行为 hello
$ sed '/aaa/chello' sed.txt  # 把包含 aaa 的行修改为 hello
$ sed '$chello' sed.txt  # 把最后一行修改为 hello
```

- 内容替换：`s`

```bash
$ sed 's/aaa/hello/' sed.txt  # 把每行的第一个 aaa 修改为 hello
$ sed 's/aaa/hello/g' sed.txt  # 全局匹配替换
$ sed 's/aaa/hello/2' sed.txt  # 把每行的第二个 aaa 修改为 hello
```

- 替换写入：`w`

```bash
# 把每行的第二个 aaa 修改为 hello 同时把第二行写入到 sed2.txt 文件
$ sed 's/aaa/hello/2w sed2.txt' sed.txt

$ sed -n 's/aaa/hello/2pw sed2.txt' sed.txt  # n 和 p 同时使用，可以只打印修改的行

$ sed '/i/s/t.*//' sed.txt  # 找到包含 i 的行，把行中 t 后面的内容置为空
```

- 拼接：`&`

```bash
$ sed 's/$/&test/' sed.txt  # 在每一行末尾拼接 test
$ sed 's/^/&#/'  sed.txt  # 每一行行首拼接 #
```

### 查询数据

```bash
$ sed -n '/aaa/p' sed.txt  # 查询含有 aaa 的行
```

### 多个 sed 命令执行

```bash
$ sed -e '1d' -e 's/aaa/hello/g' sed.txt  # 删除第一行，同时把所有的 aaa 替换成 hello
# 或者
$ sed '1d;s/aaa/hello/g' sed.txt
```

## 缓存区数据交换

- 模式空间：sed 把文件按行读出所存放的空间，会对该空间的内容做处理
- 暂存空间：额外的空间，默认开始有一个空行。

sed 可以使用命令相互交换模式空间与暂存空间内容，最终展示的都是模式空间数据

| 命令 | 描述                                           |
| ---- | ---------------------------------------------- |
| `h`  | 将模式空间里面的内容复制到暂存空间（覆盖方式） |
| `H`  | 将模式空间里面的内容复制到暂存空间（追加方式） |
| `g`  | 将暂存空间里面的内容复制到模式空间（覆盖方式） |
| `G`  | 将暂存空间里面的内容复制到模式空间（追加方式） |
| `x`  | 交换两个空间内容                               |

案例：

- 将模式空间第一行复制到暂存空间（覆盖方式），并将暂存空间的内容复制到模式空间中的最后一行（追加方式）

```bash
$ sed '1h;$G' sed.txt
```

- 将模式空间第一行复制到暂存空间（覆盖方式）并删除，并将暂存空间的内容复制到模式空间中的最后一行（追加方式）

```bash
$ sed '1{h;d};$G' sed.txt
```

- 将模式空间第一行复制到暂存空间（覆盖方式），最后将暂存空间的内容复制到模式空间中的替换从第 2 行开始到最后一行的每行数据（覆盖方式）

```bash
$ sed '1h;2,$g' sed.txt
```

- 将前三行复制到暂存空间（追加方式），之后将暂存空间的内容复制到到模式空间中的最后一行（追加方式）

```bash
$ sed '1,3H;$G' sed.txt
```

- 给每一行添加空行

```bash
$ sed 'G' sed.txt  # 由于暂存区默认有空行。直接使用 G
```

- 删除所有的空行

```bash
$ sed '/^$/d' sed.txt
```

# awk

awk 是把输入（标准输入、文件或其他命令的输出）按行读入，以空格为默认分隔符将每行切片，切片的部分在进行分析。

语法结构：

```bash
awk [options] 'pattern{action}' {filenames}
```

常用选项：

- `-F`：定义字段分隔符，默认空格
- `-v`：定义变量并赋值

## awk 内部变量

| 变量            | 变量说明                               | 备注                                                         |
| --------------- | -------------------------------------- | ------------------------------------------------------------ |
| `$0`            | 当前处理行的所有记录                   | `awk '{print $0}' demo.txt` 打印所有行                       |
| `$1,$2,$3...$n` | 文件中每行以**间隔符号**分割的不同字段 | `awk -F: '{print $1,$3}'`打印以冒号分割的第一列和第三列      |
| `NF`            | 当前记录的字段数（列数）               | `awk -F: '{print NF}'`                                       |
| `$NF`           | 最后一列                               | `$(NF-1)`表示倒数第二列                                      |
| `FNR/NR`        | 行号                                   | `awk 'NR==1,NR==3{print $0}' demo.txt`打印 1-3 行<br>`awk 'NR==1 || NR==3{print $0}' demo.txt`打印第 1或3 行<br>`awk 'NR>=3 && NR<=5{print $0}' demo.txt`打印大于等于 3 小于等于 5 的行 |
| `FS`            | 定义输入间隔符，等价于`-F`             | `awk 'BEGIN{FS=":";OFS="\t"};{print $1,$3}' demo.txt`        |
| `OFS`           | 定义输出字段分隔符，默认空格           | `'awk -F: 'BEGIN{OFS="\t"};{print $1,$3}' demo.txt`打印第以冒号分割的第一列和第三列，每列之间用 `\t` 分割<br>`awk -F: '{print $1"\t"$3}' demo.txt`与上面同理 |
| `RS`            | 行输入结束分割符，默认换行             | `awk -F: 'BEGIN{RS="\t"};{print $0}' demo.txt`按 `\t` 为分隔符作为输入的一行 |
| `ORS`           | 行输出结束分割符，默认换行             | `awk -F: 'BEGIN{ORS="\n\n"};{print $1,$3}' demo.txt`按 `\n\n` 为分隔符作为输出的一行 |
| `FILENAME`      | 当前输入的文件名                       |                                                              |

## 格式化输出`print`和`printf`

```bash
# print 函数		类似 echo "hello world"
$ date | awk '{print "Month: "$3"\nYear: "$4}'
Month: Dec
Year: 2020

# printf 函数		类似 echo -n
# - %s   字符类型
# - %d   数值类型	
# - %15  占15字符
# - %-15 表示左对齐，默认是右对齐
# printf 默认不会在行尾自动换行，加\n
$ date
Thu 31 Dec 2020 10:54:21 PM CST

$ date | awk  '{printf "|%-15s|%-10s|%-15s|\n", $1,$2,$3}'  # 左对齐
|Thu            |31        |Dec            |

$ date | awk  '{printf "|%15s|%10s|%15s|\n", $1,$2,$3}'  # 右对齐
|            Thu|        31|            Dec|
```

## 变量定义

```bash
$ awk -v NUM=3 -F: '{ print NUM }' demo.txt  # NUM 不能加 $，demo.txt 不具有意义
3
3
3

$ awk -v NUM=3 '{ print NUM }'  # 一次回车，输出一个 3

3

3

3
```

##  流程控制语句

```bash
awk -F: '{if($3>=500 && $3<=60000) {print $1,$3} }' /etc/passwd

$ awk -F: '{if($3==0) {print $1"是管理员"} }' /etc/passwd 
root是管理员

$ awk 'BEGIN{if('$(id -u)'==0) {print "root"} }' # id -u 获取 uid
root

$ awk -F: '{ if($3>=500 && $3 != 65534) {print $1"是普通用户"} else {print $1,"不是普通用户"}}' /etc/passwd
root 不是普通用户
daemon 不是普通用户
bin 不是普通用户
...

$ awk 'BEGIN{if( '$(id -u)'>=500 && '$(id -u)' !=65534 ) {print "是普通用户"} else {print "不是普通用户"}}'
不是普通用户

$ awk -F: '{ if($3==0) {print $1,":是管理员"} else if($3>=1 && $3<=499 || $3==65534 ) {print $1,":是系统用户"} else {print $1,":是普通用户"}}' /etc/passwd
root :是管理员
daemon :是系统用户
bin :是系统用户
...

$ awk -F: '{ if($3==0) {i++} else if($3>=1 && $3<=499 || $3==65534 ) {j++} else {k++}};END{print "管理员个数为:"i "\n系统用户个数为:"j"\n普通用户的个数为:"k }' /etc/passwd
管理员个数为:1
系统用户个数为:28
普通用户的个数为:1
```

## 循环语句

```bash
$ awk 'BEGIN { for(i=1;i<=5;i++) {print i} }'
1
2
3
4
5

$ awk 'BEGIN { i=1;while(i<=5) {print i;i++} }'
1
2
3
4
5

:<<!
for(y=1;y<=5;y++){
	for(x=1;x<=y;x++){
		printf x
	}
	prtint
}
!
$ awk 'BEGIN{ for(y=1;y<=5;y++) {for(x=1;x<=y;x++) {printf x} ;print } }'
1
12
123
1234
12345
```

