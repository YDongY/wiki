---
title: Ubuntu 20.04 Docker 部署 Wiki.js
description: 
published: true
date: 2021-02-21T14:37:30.170Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:37:27.635Z
---

# Docker-Compose 安装 Wiki.js

> [官方文档](https://docs.requarks.io/install/docker)

```yml
version: "3"
services:

  db:
    image: postgres:11-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "80:3000" # 端口映射
    volumes:
      - wiki-data:/wiki

volumes:
  db-data:
  wiki-data:
```

- 启动

```shell
docker-compose up # 前台启动
docker-compose up -d # 守护进程启动
```

```shell
$ docker-compose up
Creating network "wiki_default" with the default driver
Creating volume "wiki_db-data" with default driver
Creating volume "wiki_wiki-data" with default driver
Creating wiki_db_1 ... done
Creating wiki_wiki_1 ... done
Attaching to wiki_db_1, wiki_wiki_1
db_1    | WARNING: no logs are available with the 'none' log driver
wiki_1  | Loading configuration from /wiki/config.yml... OK
wiki_1  | 2021-02-21T03:06:54.696Z [MASTER] info: =======================================
wiki_1  | 2021-02-21T03:06:54.699Z [MASTER] info: = Wiki.js 2.5.170 =====================
wiki_1  | 2021-02-21T03:06:54.700Z [MASTER] info: =======================================
wiki_1  | 2021-02-21T03:06:54.701Z [MASTER] info: Initializing...
wiki_1  | 2021-02-21T03:06:55.223Z [MASTER] info: Using database driver pg for postgres [ OK ]
wiki_1  | 2021-02-21T03:06:55.245Z [MASTER] info: Connecting to database...
wiki_1  | 2021-02-21T03:06:55.369Z [MASTER] info: Database Connection Successful [ OK ]
wiki_1  | 2021-02-21T03:06:55.826Z [MASTER] warn: DB Configuration is empty or incomplete. Switching to Setup mode...
wiki_1  | 2021-02-21T03:06:55.827Z [MASTER] info: Starting setup wizard...
wiki_1  | 2021-02-21T03:06:55.987Z [MASTER] info: Starting HTTP server on port 3000...
wiki_1  | 2021-02-21T03:06:55.988Z [MASTER] info: HTTP Server on port: [ 3000 ]
wiki_1  | 2021-02-21T03:06:55.992Z [MASTER] info: HTTP Server: [ RUNNING ]
wiki_1  | 2021-02-21T03:06:55.993Z [MASTER] info: 🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻🔻
wiki_1  | 2021-02-21T03:06:55.994Z [MASTER] info: 
wiki_1  | 2021-02-21T03:06:55.994Z [MASTER] info: Browse to http://YOUR-SERVER-IP:3000/ to complete setup!
wiki_1  | 2021-02-21T03:06:55.995Z [MASTER] info: 
wiki_1  | 2021-02-21T03:06:55.995Z [MASTER] info: 🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺🔺
```

- 停止

```shell
docker-compose stop
```

- 查看数据卷

```shell
docker volume ls

docker volume inspect [VOLUME NAME] # 查看数据卷详细信息
```

```shell
$ docker volume inspect wiki_wiki-data
[
    {
        "CreatedAt": "2021-02-21T11:06:50+08:00",
        "Driver": "local",
        "Labels": {
            "com.docker.compose.project": "wiki",
            "com.docker.compose.version": "1.25.0",
            "com.docker.compose.volume": "wiki-data"
        },
        "Mountpoint": "/var/lib/docker/volumes/wiki_wiki-data/_data",
        "Name": "wiki_wiki-data",
        "Options": null,
        "Scope": "local"
    }
]
```

# 配置 Git 备份

```
Authentication Type    : basic
Repository URL         : https://github.com/xxx/xxx.git
Branch                 : master
Username               : Demo
Password / PAT         : Demo123
Default Author Email   : example.example.com
Default Author Name    : Demo
Local Respository Path : ./data/repo # 可以通过数据卷查看
```




















