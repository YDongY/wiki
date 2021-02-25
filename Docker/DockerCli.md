---
title: Docker Cli
description: 
published: true
date: 2021-02-21T14:36:36.224Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:36:33.308Z
---

# 1. 初始Docker

![](/downloads/docker/docker-cli.png)

# 2. 镜像命令

## 2.1 列出本地主机上的镜像信息

```bash
$ docker images [options]

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
```

- `REPOSITORY`：镜像的仓库源
- `TAG`：镜像的版本标签
- `IMAGE ID`：镜像ID
- `CREATED`：镜像创建时间
- `SIZE`：镜像大小

可选参数（可以组合使用）：

- `a`：列出本地所有的镜像（含中间映像层）
- `q` ：只显示镜像ID
- `-digests` ： 显示镜像的摘要信息
- `-no-trunc`：显示完整的镜像信息

帮助命令：

```bash
$ sudo docker images --help

Usage:	docker images [OPTIONS] [REPOSITORY[:TAG]]

List images

Options:
  -a, --all             Show all images (default hides intermediate images)
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs
```

## 2.2 查询镜像

查询源来自 `http://hub.docker.com`

```bash
$ sudo docker search mysql　# 镜像名

NAME              DESCRIPTION                  STARS      OFFICIAL    AUTOMATED
mysql             MySQL is a widely used,....  9682         [OK]
mariadb           MariaDB is a community.....  3523         [OK]
............
```

### 2.2.1 更多搜索

- 搜索`stars`超过 20 的，通过列名过滤

```bash
$ sudo docker search mysql --filter=stars=20
```

- 最多搜索10条(默认25条)

```bash
$ sudo docker search mysql --limit 10
```

## 2.3 下载镜像

```bash
$ docker pull image[:TAG] # 默认：docker pull 镜像名:latest,版本可选

# eg:sudo docker pull mysql
```

## 2.4 删除镜像

```bash
$ docker rmi 镜像ID[:TAG]
```

### 2.4.1 强制删除 `-f`

```bash
$ sudo docker rmi -f bf756fb1ae65
Untagged: hello-world:latest
Untagged:helloworld@sha256:d58e752213a51785838f9eed2b7a498ffa1cb3aa7f946dda11af39286c3db9a9
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```

### 2.4.2 删除多个

```bash
$ docker rmi -f 镜像名1 镜像名2
```

### 2.4.3 删除全部

```bash
$ docker rmi -f $(docker images -qa)
```

# 3. 容器命令

## 3.1 新建并启动容器

- **options:**
    - `-name="容器新名字"`: 为容器指定一个名称；
    - `d`: 后台运行容器，并返回容器ID，也即启动**守护式容器**；
    - `i`：**以交互模式运行容器，通常与 -t 同时使用**；
    - `t`：**为容器重新分配一个伪输入终端，通常与 -i 同时使用**；
    - `e`：环境变量
    - `P`: 随机端口映射；
    - `p`: 指定端口映射，有以下四种格式
        - `ip:hostPort:containerPort`
        - `ip::containerPort`
        - `hostPort:containerPort`
        - `containerPort`

```bash
$ docker run -it 镜像ID
```

## 3.2 列出当前所有正在运行的容器

- **options:**
    - `a` ：列出当前所有正在运行的容器+历史上运行过的
    - `l` ：显示最近创建的容器。
    - `n 10`：显示最近创建的10个容器。
    - `q`：静默模式，只显示容器编号。
    - `-no-trunc`：不截断输出。

```bash
$ sudo docker ps
CONTAINER ID    IMAGE     COMMAND      CREATED             STATUS         PORTS      NAMES
1219aae1f4a8    ubuntu    "/bin/bash"  42 seconds ago     Up 38 seconds            cool_kilby
```

## 3.3 退出容器

```bash
$ exit  ：容器停止运行退出
$ ctrl+P+Q :容器不停止退出
```

## 3.4 重启容器

```bash
$ docker restart 容器ID
```

## 3.5 停止容器

```bash
$ docker stop 容器ID
```

## 3.6 强制停止

```bash
$ docker kill 容器ID
```

## 3.7 删除已停止的容器

```bash
docker rm 容器ID
docker rm $(docker ps -a -q)  #删除多个容器
docker ps -a -q | xrags docker rm #删除多个容器
```

# 4.重要容器命令

## 4.1 启动守护式进程，通过`ps`查看

```bash
$ docker run -d 镜像ID

# >>>>>>>>>>>>>>>>>>>>>>>>>>
$ sudo docker run -d ubuntu
12a960dcce1bf7af5f162faad7159ec094fc924db26149f86fd7ba0b528d3baf
```

## 4.2 查看容器日志

- **options**
    - `t` ：加入时间戳
    - `f`： 跟随最新的日志打印
    - `tail 30`：显示最后30条

```bash
$ docker logs -f -t --tail 3 容器ID
```

## 4.3 查看容器内运行的进程

```bash
$ docker top 容器ID
```

## 4.4 查看容器内部细节

```
$ docker inspect 容器ID
```

## 4.5 进入正在运行的容器并以命令行交互

```bash
$ docker exec -it 容器ID bashShell
$ docker attach 容器ID
```

- `attach`：直接进入容器启动命令的终端，不会启动新的进程
- `exec`：可以直接在宿主机执行容器类的程序，例如以下：在宿主机查看`ubuntu`容器根目录的文件

```bash
$ sudo docker exec -t 71b ls -l /
total 48
lrwxrwxrwx   1 root root    7 Jun  6 01:18 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 15 11:09 boot
drwxr-xr-x   5 root root  360 Jun 27 10:28 dev
drwxr-xr-x   1 root root 4096 Jun 27 10:28 etc
drwxr-xr-x   2 root root 4096 Apr 15 11:09 home
......
```

## 4.6 从容器内拷贝文件到主机上

```bash
$ docker cp 容器ID:容器内路径 目的主机路径
```

# 5. 镜像`Commit`

类似于 git 的 commit，假如我们的某个镜像启动后，在产生的容器中进行了一些配置的修改，而我们想要保存本次配置过得容器就可以利用它为模板 commit 一个配置过后的镜像，以后启动当前镜像就已经是配置过后的容器

```bash
$ docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]

# eg:commit一个安装过 vim 的 ubuntu 镜像，名称为：atguigu/ubuntu，标签 TAG 为：1.2
$ sudo docker commit -m="install vim" -a="ydongy" 52ca02c8b0c9 atguigu/ubuntu:1.2
```

把配置后的镜像打包成压缩文件

```shell
$ docker save -o xxx.tar images_name:tag

# eg:docker save -o my_mysql.tar my_mysql:1.0
```

从压缩文件还原镜像

```shell
$ docker load -i xxx.tar
```

# 6. Docker容器卷

容器的持久化，即使容器停止运行，宿主机修改的数据，在容器重新启动运行后仍然能够得到数据宿主机与容器之间数据共享

## 6.1 数据卷

### 6.1.1 命令添加

```bash
$ docker run -it -v /宿主机绝对路经目录:/容器内目录　镜像名

# eg:宿主机的/data/docker-ubuntu目录与容器ubuntu的/MyData目录建立数据共享
$ sudo docker run -it -v /data/docker-ubuntu:/MyData ubuntu

# eg:只读权限，只需要加一个ro，代表read-only
$ sudo docker run -it -v /data/docker-ubuntu:/MyData:ro ubuntu

# 如果存在写权限有问题，可加后缀：--privileged=true
```

### 6.1.2 DockerFile 添加

```docker
# volume test
FROM ubuntu
VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]　# 在容器中建立两个数据卷，宿主机可以查看内部细节
CMD echo "finished,--------success1"
CMD /bin/bash
```

通过`build`构建一个新的镜像`ydongy/ubuntu`，**注意结尾的点**

```bash
$ sudo docker build -f /data/docker-ubuntu/Dockerfile -t ydongy/ubuntu . # 注意最后一个点
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM ubuntu
 ---> 74435f89ab78
Step 2/4 : VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
 ---> Running in 2be2fbf7b5fd
Removing intermediate container 2be2fbf7b5fd
 ---> a9434af08c1a
Step 3/4 : CMD echo "finished,--------success1"
 ---> Running in 6ce8192d90f7
Removing intermediate container 6ce8192d90f7
 ---> 6272fe09d969
Step 4/4 : CMD /bin/bash
 ---> Running in 7b26a6661dce
Removing intermediate container 7b26a6661dce
 ---> be0d288d5a6f
Successfully built be0d288d5a6f
Successfully tagged ydongy/ubuntu:latest
```

## 6.2 数据卷容器

命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器。一句话：继承关系，一个容器继承另一个容器，挂载数据卷可以实现共享

6.2.1 启动一个容器

```bash
$ docker run -it --name father ubuntu
```

6.2.2 启动一个子容器

```bash
$ docker run -it --name son --volumes-from father ubuntu
```

两个容器对数据卷进行修改时，可以实现共享数据。如果存在多个子容器继承一个父容器，所以的容器都可以共享数据，哪怕把父容器给删除了，剩余的子容器之间仍然能够共享。