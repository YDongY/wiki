---
title: Docker 安装 MySQL
description: 
published: true
date: 2021-02-21T13:36:13.818Z
tags: 
editor: undefined
dateCreated: 2021-02-21T09:30:04.839Z
---

# 1. 查看可用版本

```bash
$ docker search mysql

NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9942                [OK]
mariadb                           MariaDB is a community-developed fork of MyS…   3636                [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   724                                     [OK]
percona                           Percona Server is a fork of the MySQL relati…   508                 [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   83
```

# 2. 拉取 MySQL 镜像

```bash
# $ docker pull image[:TAG] # 默认：docker pull 镜像名:latest,版本可选
$ docker pull mysql
```

# 3. 查看本地镜像

```bash
$ docker images

# >>>>>>>>>>>>>
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              3646af3dc14a        3 days ago          544MB
hello-world         latest              fce289e99eb9        20 months ago       1.84kB
```

# 4. 运行容器

```bash
# 映射容器3306端口到宿主机3306端口，登录密码为 123456，名称为 mysql-test ，交互式守护进程打开

$ docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

# 5. 查看是否运行

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
e8a124ed786d        mysql               "docker-entrypoint.s…"   24 minutes ago      Up 7 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp   mysql-test
```

# 6. 交互进入 MySQL 容器

```bash
$ docker exec -it 容器ID bash
```

# 7. 容器内连接访问

```bash
$ mysql -uroot -p123456
```

# 8. 宿主机访问

```bash
$ ifconfig

docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:bf:67:12:90  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ mysql -h 172.17.0.1 -uroot -p123456
```

# 9. 容器卷配置

## 9.1 `/opt` 目录下新新建文件夹

```bash
$ mkdir -p /opt/docker_v/mysql
$ mkdir -p /opt/docker_v/mysql/logs
$ mkdir -p /opt/docker_v/mysql/data
```

## 9.2 目录下新建配置文件

```bash
$ cd /opt/docker_v/mysql
$ touch my.cnf

[mysqld]
user=mysql
character-set-server=utf8
default_authentication_plugin=mysql_native_password
secure_file_priv=/var/lib/mysql
expire_logs_days=7
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
max_connections=1000

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8
```

## 9.3 启动

```bash
$ docker run -d --privileged=true \
-v /opt/docker_v/mysql/conf:/etc/mysql \
-v /opt/docker_v/mysql/logs:/var/log/mysql \
-v /opt/docker_v/mysql/data:/var/lib/mysql \
-v /opt/docker_v/mysql/my.cnf:/etc/mysql/my.cnf \
-p 33060:3306 --name=mysql -e MYSQL_ROOT_PASSWORD=123456 \
<镜像id>
```

# 参考

- [https://hub.docker.com/_/mysql?tab=tags](https://hub.docker.com/_/mysql?tab=tags)
- [https://www.runoob.com/docker/docker-install-mysql.html](https://www.runoob.com/docker/docker-install-mysql.html)

