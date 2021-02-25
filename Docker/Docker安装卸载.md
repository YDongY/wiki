---
title: Docker 安装卸载
description: 
published: true
date: 2021-02-21T14:36:43.906Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:36:41.313Z
---

> [官方教程 Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
{.is-info}


# 1. 卸载

- 查看是否安装过 Docker

```bash
docker --version
dpkg -l | grep docker
```

- 卸载旧 Docker

```bash
$ sudo apt-get remove docker docker-engine docker.io containerd runc
$ sudo apt autoremove docker* --purge
```

# 2. Ubuntu 安装

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


1. 阿里云

    ```bash
    $ curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

    $ sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
    ```

2. 中国科技大学

    ```bash
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

>Docker 的官方 GPG 密钥 `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

- 安装Docker-ce

```bash
$ sudo apt install docker-ce
```

- 设置开机自启动并启动 Docker-ce（安装成功后默认已设置并启动，可忽略）

```bash
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

- 验证是否安装成功

```bash
$ docker -v

>>> Docker version 19.03.12, build 48a66213fe
```

# 3. 镜像加速

[阿里云登录 - 欢迎登录阿里云，安全稳定的云计算服务平台](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

## 3.1 阿里云镜像加速

- 登录阿里云[容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instances/repositories)，点击镜像加速

![](/downloads/docker/install/阿里云镜像.png)

- 配置镜像加速地址

![](/downloads/docker/install/阿里云镜像2.jpg)

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

![](/downloads/docker/install/阿里云镜像3.png)

## 3.2 网易云镜像加速

- 直接修改`/etc/docker/daemon.json`:

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

# 4. Deepin 中安装 Docker

> [https://wiki.deepin.org/wiki/Docker](https://wiki.deepin.org/wiki/Docker)
{.is-info}

# 5. Docker 私有仓库

## 5.1 私有仓库搭建

```shell
# 1、拉取私有仓库镜像 
docker pull registry

# 2、启动私有仓库容器 
docker run -id --name=registry -p 5000:5000 registry

# 3、打开浏览器 输入地址http://私有仓库服务器ip:5000/v2/_catalog，看到
{"repositories":[]} # 表示私有仓库 搭建成功

# 4、修改daemon.json   
vim /etc/docker/daemon.json    

# 在上述文件中添加一个key，保存退出。此步用于让 docker 信任私有仓库地址；注意将私有仓库服务器 ip 修改为自己私有仓库服务器真实 ip 
{"insecure-registries":["私有仓库服务器ip:5000"]}

# 5、重启docker 服务 
systemctl restart docker
docker start registry
```

## 5.2 将镜像上传至私有仓库

```shell
# 1、标记镜像为私有仓库的镜像     
docker tag xxx 私有仓库服务器IP:5000/xxx
 
# 2、上传标记的镜像     
docker push 私有仓库服务器IP:5000/xxx
```

## 5.3 从私有仓库拉取镜像 

```shell
# 拉取镜像 
docker pull 私有仓库服务器ip:5000/xxx
```
