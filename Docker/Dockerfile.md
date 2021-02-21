---
title: Dockerfile
description: 
published: true
<<<<<<< HEAD
date: 2021-02-21T13:36:25.828Z
tags: 
editor: undefined
dateCreated: 2021-02-21T09:37:48.590Z
=======
date: 2021-02-21T14:36:40.128Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:36:37.478Z
>>>>>>> docs: add all untracked content
---

Dockfile 是用来构建 Docker 镜像的构建文件，是由一系列命令和参数构成的脚本

# 1.构建过程

1. 每条保留字指令都必须为大写且后面至少跟随一个参数
2. 指令从上到下，顺序执行
3. `#` 表示注释
4. 每条指令都会创建一个新的镜像层，并对镜像进行提交

# 2.构建命令

```shell
$ docker build -t 新镜像名字:TAG .

# eg: sudo docker build -f /data/docker-ubuntu/Dockerfile -t ydongy/ubuntu . 
```

# 3.保留关键字

## 3.1 `FROM`

> 当前镜像基于那个镜像，类似于导包

```dockerfile
FROM <image>
FROM <image>:<tag>
FROM <image>:<digest>
# <tag>和<digest> 是可选项，如果没有选择，那么默认值为latest

# eg:From ubuntu:20.04
```

## 3.2 `MAINTAINER`

> 镜像的作者和邮箱地址

```dockerfile
MAINTAINER <name> <email>

# eg:MAINTAINER tom "example@example.com"
```

## 3.3 `RUN`

> 容器构建时需要运行的命令，换行符是`\`

```dockerfile
# 在linux操作系统上默认 /bin/sh -c
# 在windows操作系统上默认 cmd /S /C
RUN <command>

# eg:RUN echo 'hello docker!' > /usr/local/file.txt

# 可将executable理解成为可执行文件，后面就是两个参数。
RUN ["executable", "param1", "param2"] 

# eg:RUN ["/bin/bash", "-c", "echo hello"]
```

## 3.4 `EXPOSE`

> 当前镜像容器对外暴露的端口

```dockerfile
EXPOSE 8080 
EXPOSE 3306
```

## 3.5 `WORKDIR`

> 指定创建容器后，终端默认登录进来的目录。之后的命令都是基于此工作目录，如果不存在，则会创建目录。

```dockerfile
WORKDIR /usr/local
RUN echo 'hello docker' > text.txt

# 最终会在/usr/local/目录下生成text.txt文件
```

## 3.6 `ENV`

> 用于构建镜像过程中设置的环境变量

```dockerfile
ENV MY_PATH /usr/mytest

# 这个环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样；也可以在其它指令中直接使用这些环境变量，

# eg:WORKDIR $MY_PATH
```

## 3.7 `ADD`

> 将宿主机目录的文件拷贝到镜像，且`ADD`命令会自动处理`URL`和解压缩(`tar, gzip, bzip2, etc`)

```dockerfile
ADD centos-7-docker.tar.xz
```

## 3.8 `COPY`

> 将主机的文件复制到镜像内，如果目的位置不存在，Docker会自动创建所有需要的目录结构，但是它只是单纯的复制，并不会去做文件提取和解压工作。

```dockerfile
COPY src dest
COPY ["src","dest"]
# eg:COPY config.yml /etc/service/src/resources
```

## 3.9 `VOLUME`

> 指定容器数据卷，用于数据保存和持久化工作

```dockerfile
VOLUME ["xxx","xxx"...]
```

## 3.10 `CMD`

> 指定一个容器启动时要运行的命令，当有多个`CMD`的时候，只有最后一个生效。

```dockerfile
CMD <命令>

# eg:CMD /bin/bash

CMD ["可执行文件","参数1","参数2"]

# eg:CMD ["/bin/bash"]
```

## 3.11 `ENTRYPOINT`

> 用法与`CMD`一致

`CMD`的命令会被` docker run `的命令覆盖而`ENTRYPOINT`不会。如使用`CMD ["/bin/bash"]`或`ENTRYPOINT ["/bin/bash"]`后，再使用`docker run -ti image`启动容器，它会自动进入容器内部的交互终端，如同使用
 `docker run -ti image /bin/bash`。

但是如果启动镜像的命令为`docker run -ti image /bin/ps`，使用`CMD`后面的命令就会被覆盖转而执行`bin/ps`命令，而`ENTRYPOINT`的则不会，而是会把docker run 后面的命令当做`ENTRYPOINT`执行命令的参数*。

## 3.12 `ONBUILD`

> 当一个包含`ONBUILD`命令的镜像被用作其他镜像的基础镜像时该命令就会执行。就是一个镜像包含这个命令并且想要执行一些东西，当另一个镜像`From`这个镜像，在`buile`的时候就会执行父镜像定义的东西

如创建镜像`my01/ubuntu`：

```dockerfile
# Dockerfile01
FROM ubuntu
......
ONBUILD RUN echo "========now build======="
......

# build镜像
docker build -f Dockerfile01 -t my01/ubuntu .
```

接着创建`my02/ubuntu`：

```dockerfile
# Dockerfile02
FROM my01/ubuntu
......

# build镜像
docker build -f Dockerfile02 -t my02/ubuntu .

# 构建日志
>>># Execting 1 build triggers
>>>Trigger 0,RUN echo "========now build======="
```

# 4. 综合案例

## 4.1 自定义镜像`ubuntu`

```dockerfile
FROM ubuntu

# 维护者和邮箱
MAINTAINER ydongy<ydongy97@163.com>

ENV MYPATH /usr/local

# 默认登录工作目录
WORKDIR $MYPATH

# 拷贝宿主机当前目录的test.txt到容器/usr/local/container.txt
COPY test.txt $MYPATH/container.txt

# 把redis添加到容器
ADD redis-5.0.5.tar.gz $MYPATH/

# 安装vim，net-tools
RUN apt-get update && apt-get install -y vim && apt-get install -y net-tools --fix-missing

# 容器运行时对外暴露监听的端口
EXPOSE 80

CMD echo $MYPYTH

CMD echo ">>>>>>>>>success"

CMD /bin/bash
```

- 相关参考：https://www.jianshu.com/p/10ed530766af
- 官方文档：https://docs.docker.com/engine/reference/builder/
