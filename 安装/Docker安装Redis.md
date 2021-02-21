---
title: Docker 安装 Redis
description: 
published: true
date: 2021-02-21T13:36:16.598Z
tags: 
editor: undefined
dateCreated: 2021-02-21T09:31:21.364Z
---

# 1. 查看可用版本

```shell
$ docker search redis
NAME                             DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
redis                            Redis is an open source key-value store that…   8618                [OK]                
bitnami/redis                    Bitnami Redis Docker Image                      162                                     [OK]
sameersbn/redis                                                                  81                                      [OK]
grokzen/redis-cluster            Redis cluster 3.0, 3.2, 4.0, 5.0, 6.0           71                                      

```

# 2. 拉取 Redis 镜像

```shell
$ docker pull Redis
```

# 3. 创建容器，设置端口映射

```shell
docker run -itd --name redis-test -p 6379:6379 redis
```

# 4. 通过 redis-cli 连接测试使用 redis 服务

```shell
$ docker exec -it redis-test /bin/bash
```