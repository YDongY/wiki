---
title: Docker 架构
description: 
published: true
date: 2021-03-02T11:50:57.996Z
tags: docker
editor: markdown
dateCreated: 2021-03-01T12:34:47.796Z
---

# 什么是容器

Linux 容器是与系统其他部分隔离开的一系列进程

# 容器与 Docker







# 容器生态

![容器生态.png](/assets/docker/容器生态.png)


# 什么是 Docker

Docker 最初是由 DotCloud 公司

![Container](https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-and-vm-transparent-bg.png)

经常谈起 Docker 会把它与 VM 进行比较，上图中右边是 VM 的结构图，左边是 Docker 结构图。




# Docker 架构

![docker](https://docs.docker.com/engine/images/architecture.svg)

Docker 采用 C/S 架构，



- Docker 客户端：Client


- Docker 服务器：Docker daemon

Docker daemon 就是运行在 Host 上的守护进程，负责创建、运行、监控容器、构建、存储镜像。

默认配置下，Docker 客户端只能通过 socket 与 Docker daemon 通信，当然也可以通过编辑配置文件实现远程访问（Docker 具有标准的 RESTful API）。

```shell
sudo vim /etc/systemd/system/multi-user.target.wants/docker.service
```

修改 `ExecStart` 参数如下：

```shell
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.00
```

然后重启 docker daemon ，尝试其他 Docker 客户端连接

```shell
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker.service

# 远程访问，默认端口 2375
$ sudo docker -H 192.168.33.10 info
```



https://medium.com/@saschagrunert