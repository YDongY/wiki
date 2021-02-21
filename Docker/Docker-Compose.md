---
title: Docker Compose
description: 
published: true
<<<<<<< HEAD
date: 2021-02-21T13:36:19.391Z
tags: 
editor: undefined
dateCreated: 2021-02-21T09:32:43.219Z
=======
date: 2021-02-21T14:36:32.273Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:36:29.653Z
>>>>>>> docs: add all untracked content
---

# 1. 安装Docker Compose

```shell
# 二进制包方式安装在Linux系统中。
curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 设置文件可执行权限
chmod +x /usr/local/bin/docker-compose
# 查看版本信息
docker-compose -version
```

# 2. 卸载Docker Compose

```shell
# 二进制包方式安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose
```

# 3. docker-compose cli

- 启动

```shell
sudo docker-compose up
# 容器重新编译再启动
sudo docker-compose up --build
# 后台启动
sudo docker-compose up -d --build
```

- 查看容器状态

```shell
$ sudo docker-compose ps
          Name                  Command          State     Ports
----------------------------------------------------------------
docker_tutorial_client_1   python ./client.py   Exit 0
docker_tutorial_server_1   python ./server.py   Exit 137
```

- 执行容器内命令

```shell
sudo docker-compose run flaskweb top
```

- 查看容器输出日志

```shell
sudo docker-compose logs -f flaskweb
```

- 容器停止

```shell
sudo docker-compose stop
# 容器停止并清除容器和网络
sudo docker-compose down
# 停止删除容器，网络，镜像
sudo docker-compose down --rmi all
```

- 在运行的容器中执行命令，例如：docker-compose exec server ls

```shell
docker-compose exec [service name][command]
```

- 检查镜像

```shell
docker inspect <tag or id>
```

# 4. 编写 docker-compose.yml 文件

> [https://docs.docker.com/compose/compose-file/](https://docs.docker.com/compose/compose-file/)
{.is-info}


```yml
version: "3.8"

services:

  webapp:
    # build: ./src 　　　　　　　　　　　　　　　　# 指定构建的路径
    build:
      context: ./src                          # 指定构建的路径

      dockerfile: Dockerfile                  # 指定需要构建的 dockerfile
        args:                                 # https://docs.docker.com/compose/compose-file/#args
          buildno: 1                          # 传递给 dockerfile 的参数 `$(buildno)`

    networks:
      - my-network

    command: python ./server.py               # 执行的命令

    configs:
      - my_config
      - my_other_config

    depends_on:                               # docker-compose up以依赖性顺序启动服务，先启动 nginx 再启动 webapp
      - nginx

    deploy:                                   # 部署
      -

    env_file:                                 # 从文件加载环境变量
      - .env

    environment:                              # 添加环境变量
      - DEBUG=1
      # DEBUG: 1

    expose:                                   # 指定内部暴露端口
      - "8000"

    extra_hosts:                              # 添加主机名映射
      - "somehost:162.242.195.82"
      - "otherhost:50.31.209.229"

    healthcheck:                              # 检测健康
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 1m30s
      timeout: 10s
      retries: 3
      start_period: 40s

    volumes:                                  # 卷
      - ./src:/src


  nginx:
    image: nginx                              # 指定要从启动容器的图像
    container_name: nginx                     # 自定义容器名称
    restart: always                           # 重启
    ports:                                    # 映射端口
      - "3000"
      - "3000-3005"
      - "8000:8000"
      - "9090-9091:8080-8081"
      - "49100:22"
      - "127.0.0.1:8001:8001"
      - "127.0.0.1:5000-5010:5000-5010"
      - "6060:6060/udp"
      - "12400-12500:1240"

    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"

configs:
  my_config:
    file: ./my_config.txt
  my_other_config:
    external: true

networks:
  my-network:
```