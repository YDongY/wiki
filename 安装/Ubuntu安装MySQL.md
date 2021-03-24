---
title: Ubuntu 安装 MySQL
description: 
published: true
date: 2021-03-24T15:12:18.959Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:11:49.381Z
---

# 1. 使用 APT 仓库

## 1.1 下载 MySQL APT 仓库

```shell
wget https://dev.mysql.com/get/mysql-apt-config_0.8.15-1_all.deb
```

## 1.2 安装

```shell
sudo dpkg -i mysql-apt-config_0.8.15-1_all.deb
# 更改发行版本：sudo dpkg-reconfigure mysql-apt-config
```

## 1.3 更新

```shell
sudo apt-get update
```

## 1.4 安装 MySQL

> 安装过程需要设置密码

```shell
sudo apt-get install -y mysql-community-server
```

## 1.5 验证包，ii 表示软件包已安装

```shell
dpkg -l | grep -i mysql
```

# 2. 二进制文件安装

## 2.1 从[官网](https://dev.mysql.com/downloads/mysql/)下载二进制 tar 包

```shell
cd /opt
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.21-linux-glibc2.12-x86_64.tar.xz
```

## 2.2 添加 mysql 组和 mysql 用户

```shell
sudo groupadd mysql
sudo useradd -r -g mysql -s /bin/false mysql
```

## 2.3 选择安装位置

```
cd /usr/local
```

## 2.4 解压二进制文件

```shell
sudo tar xvf /opt/mysql-8.0.21-linux-glibc2.12-x86_64.tar.xz
```

## 2.5 制作连接符

```shell
sudo ln -s mysql-8.0.21-linux-glibc2.12-x86_64 mysql
```

## 2.6 创建必要目录并将所有权限更改为 mysql

```shell
cd mysql
sudo mkdir mysql-files
sudo chmod 750 mysql-files
sudo chown -R mysql .
sudo chgrp -R mysql .
```

## 2.7 初始化 mysql 会生成一个临时密码

```shell
sudo bin/mysqld --initialize --user=mysql # eLhIa4q1lB:5
```

## 2.8 生成 SSL 的 RSA

```shell
sudo bin/mysql_ssl_rsa_setup
```

## 2.9  将二进制文件的所有权限更为 root，数据文件权限更为 mysql

```shell
sudo chown -R root .
sudo chown -R mysql data mysql-files
```

## 2.10  将启动脚本复制到 init.d

```shell
sudo cp support-files/mysql.server /etc/init.d/mysql
```

## 2.11 将 mysql 的二进制文件导出到 PATH 环境变量中

```shell
export PATH=$PATH:/usr/local/mysql/bin
```

## 2.12 安装后将在 /usr/local/mysql 中获取表中列出的目录

| 目录    | 内容                                                         |
| ------- | ------------------------------------------------------------ |
| bin     | mysqld 服务器、客户端和工具集                                |
| data    | 日志文件、数据库                                             |
| docs    | info 格式的 MySQL 手册                                       |
| man     | UNIX 手册页面                                                |
| include | 包含文件                                                     |
| lib     | 库                                                           |
| share   | 其他支持文件、包括错误信息、示例日志文件以及用于数据库安装的 SQL 语句 |

## 2.13 启动 MySQL 服务器

```shell
sudo service mysql start
或者
sudo /etc/init.d/mysql start
或者
sudo systemctl start mysqld
```

## 2.14 使用临时密码连接

```shell
mysql -uroot -peLhIa4q1lB:5
```

## 2.15 更改密码

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
```

# 3. 检查服务器状态

```shell
sudo systemctl status mysqld
# 或者
sudo /etc/init.d/mysql status
# 或者
sudo service mysql status
```

# 4. 停止运行

```shell
sudo service mysqld stop
# 或者
sudo /etc/init.d/mysql stop
# 或者
sudo systemctl stop mysqld
# 或者
mysqladmin -uroot -p shutdown
```

# 5. 基于 APT 安装卸载

## 5.1 查看 MySQL 依赖

```shell
dpkg -l | grep -i mysql
```

## 5.2 卸载 mysql-common

```shell
sudo apt-get remove mysql-community-server mysql-client
sudo apt-get autoremove --purge mysql-\* -y
sudo apt-get autoremove -y
```

## 5.3 确认软件包已卸载

```shell
dpkg -l | grep -i mysql
```

## 5.4 清楚残留数据（可选）

```shell
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

# 6. 基于 二进制安装卸载

> 二进制卸载只需要移除符号连接
{.is-info}

## 6.1 将目录更改为安装路径

```shell
cd /usr/local
```

## 6.2 检查 mysql 指向的位置

```shell
sudo ls -lh mysql
```

## 6.3 删除 mysql

```shell
sudo rm mysql
```

