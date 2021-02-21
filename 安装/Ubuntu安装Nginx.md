---
title: Ubuntu 安装 Nginx
description: 
published: true
date: 2021-02-21T13:34:18.876Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:07.713Z
---

## 1. Nginx 卸载

- 停止 nginx 服务

```shell
sudo service nginx stop

ps -ef | grep nginx
```

- 删除 nginx `–-purge` 包括配置文件

```shell
sudo apt-get --purge remove nginx
sudo apt-get autoremove
```

- 列出相关安装

```shell
dpkg --get-selections|grep nginx

sudo apt-get --purge remove nginx-common
```

- 再次执行

```shell
dpkg --get-selections|grep nginx

which nginx # 不在显示nginx
```

## 2. Nginx APT 安装【不推荐】

```shell
sudo apt-get install nginx
```

| 文件       | 描述             | 路径                  |
| ---------- | ---------------- | --------------------- |
| nginx      | 二进制可执行文件 | /usr/sbin/nginx       |
| nginx.conf | 配置文件         | /etc/nginx/nginx.conf |
| access.log | 访问日志         | /var/log/nginx        |
| error.log  | 错误日志         | /var/log/nginx        |

## 3. Nginx 编译安装【[官网](https://nginx.org/en/download.html)】

- 下载解压

```shell
cd /opt
sudo wget https://nginx.org/download/nginx-1.18.0.tar.gz
sudo tar -zxvf nginx-1.18.0.tar.gz
```

- 编译

```shell
cd /opt/nginx-1.18.0
sudo ./configure --prefix=/opt/nginx
```

> --prefix 将文件编译到 /opt/nginx 目录下，可以不指定 --prefix ，默认 /usr/local/nginx，当然还有其他模块参数，可以自行 Google

- make 编译

```shell
$ make
make: *** 没有规则可制作目标“build”，由“default” 需求。 停止。
```

上面错误表示缺少依赖库，执行下面命令安装

```shell
# gcc g++
apt-get install build-essential
apt-get install libtool

# pcre
sudo apt-get install libpcre3 libpcre3-dev

# zlib
apt-get install zlib1g-dev

# ssl
apt-get install openssl
apt-get install libssl-dev
```

- 下一步执行 make install 进行安装

```shell
make
make install
```

- 安装完成文件会生成在 --prefix 指定的参数位置，本示例在 /usr/local/nginx

```shell
$ tree
.
├── conf
│   ├── fastcgi.conf
│   ├── fastcgi.conf.default
│   ├── fastcgi_params
│   ├── fastcgi_params.default
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types
│   ├── mime.types.default
│   ├── nginx.conf
│   ├── nginx.conf.default
│   ├── scgi_params
│   ├── scgi_params.default
│   ├── uwsgi_params
│   ├── uwsgi_params.default
│   └── win-utf
├── html
│   ├── 50x.html
│   └── index.html
├── logs
└── sbin
    └── nginx

4 directories, 18 files
```

## 4. Nginx 启动运行

```shell
/usr/local/nginx/sbin/nginx # 默认加载 /usr/local/nginx/conf/nginx.conf 配置文件

# 建立软连接 : ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
```

- 重新加载配置

```shell
/opt/nginx/sbin/nginx -s reload
```

- 停止运行

```shell
/opt/nginx/sbin/nginx -s stop
```
