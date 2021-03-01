---
title: Docker 安装卸载
description: 
published: true
date: 2021-03-01T11:31:14.199Z
tags: 安装, docker
editor: markdown
dateCreated: 2021-02-21T14:36:41.313Z
---

> [官方教程 Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
{.is-info}


# 1. 卸载

- 查看是否安装过 Docker

```shell
$ docker --version
$ dpkg -l | grep docker
```

- 卸载旧 Docker

```shell
$ sudo apt-get remove docker docker-engine docker.io containerd runc
$ sudo apt autoremove docker* --purge
```

- 删除所有镜像，容器和卷

```shell
$ sudo rm -rf /var/lib/docker
```

# 2. Ubuntu 安装

## 2.1 脚本

```shell
$ wget -qO- https://get.docker.com/ | sh
$ sudo usermod -aG docker vagrant # 添加 vagrant 用户到 Docker 组
```

## 2.2 APT

- 安装需要的包

```bash
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

- 添加 GPG 密钥，并添加 Docker-ce 软件源，这里还是以阿里云&中国科技大学的 Docker-ce 源为例

1. 官方

    ```shell
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

2. 阿里云

    ```shell
    $ curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

    $ sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
    ```

3. 中国科技大学

    ```shell
    $ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

    $ sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \$(lsb_release -cs) stable"
    ```

3. 验证秘钥

    ```bash
    $ sudo apt-key fingerprint 0EBFCD88

    pub   rsa4096 2017-02-22 [SCEA]
        9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```


- 安装最新版本的 Docker Engine

```bash
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

- 设置开机自启动并启动 Docker-ce（安装成功后默认已设置并启动，可忽略）

```shell
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

- 验证是否安装成功

```shell
$ docker -v
```

- 开机自启，默认自启

```shell
$ sudo systemctl enable docker
$ sudo systemctl is-enabled docker # 查看是否自启
```

# 3. 镜像加速

[阿里云登录 - 欢迎登录阿里云，安全稳定的云计算服务平台](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

## 3.1 阿里云镜像加速

- 登录阿里云[容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instances/repositories)，点击镜像加速

![阿里云镜像.png](/assets/docker/阿里云镜像.png)

- 配置镜像加速地址

![阿里云镜像.png](/assets/docker/阿里云镜像2.jpg)

可以使用他的命令，也可以自己通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```json
{
  "registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
}
```

- 重启服务

```json
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

- 查看配置是否生效

```json
$ docker info
```

![阿里云镜像3.png](/assets/docker/阿里云镜像3.png)

## 3.2 网易云镜像加速

- 直接修改`/etc/docker/daemon.json`:

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

## 3.3 DaoCloud 加速

> [官方地址](https://dashboard.daocloud.io/packages/explore)
{.is-success}


# 4. Deepin 中安装 Docker

> [https://wiki.deepin.org/wiki/Docker](https://wiki.deepin.org/wiki/Docker)
{.is-info}
