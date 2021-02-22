---
title: Python 环境安装
description: 
published: true
date: 2021-02-22T08:52:17.143Z
tags: python, 安装
editor: markdown
dateCreated: 2021-02-22T08:48:00.762Z
---

# Python 安装

## 命令方式

- 安装

```shell
sudo apt-get install python3
sudo apt-get install python3-pip
sudo apt-get install python3-venv
```

- 卸载

```shell
sudo apt-get purge python3
```

## 源码方式

- 安装

> [官方下载](https://www.python.org/downloads/)
{.is-info}

```shell
# 下载
wget https://www.python.org/ftp/python/3.7.10/Python-3.7.10.tgz

# 解压
tar zxvf Python-3.7.10.tgz

# 编译
# --prefix:指定安装路径，默认 /usr/local/bin /usr/local/lib
./configure --enable-optimizations --prefix=[安装目录绝对路径]

# 安装
make
make install
```

- 卸载

```shell
sudo rm -rf [安装目录]
```

# 虚拟环境

## Virtualenv

```shell
# 下载
sudo pip install virtualenv -i https://pypi.doubanio.com/simple
sudo pip install virtualenvwrapper -i https://pypi.doubanio.com/simple

# 更改环境变量 ~/.bashrc
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh

# 激活环境变量
source ~/.bashrc 
```

### 基本使用

```shell
# 创建虚拟环境，默认 Python2
mkvirtualenv env_name
Eg：mkvirtualenv flask_py2

# 创建 Python3 虚拟环境
mkvirtualenv -p python3 flask_py3

# 进入虚拟环境
workon flask_py3

# 退出虚拟环境
deactivate

# 删除虚拟环境
rmvirtualenv flask_py3
```

## venv 

```shell
# 安装，默认安装 pip
python3 -m venv [虚拟环境名称]

eg: python3 -m venv my_venv

# 不安装 pip
python3 -m venv --without-pip myvenv

# 进入 my_venv 强制安装更新 pip 【激活之后】
./python3 -m ensurepip --upgrade

# 激活
source venv/bin/activate
```



