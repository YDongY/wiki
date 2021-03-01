---
title: Docker Cli
description: 
published: true
date: 2021-03-01T12:20:51.497Z
tags: docker
editor: markdown
dateCreated: 2021-02-21T14:36:33.308Z
---

# 1. Docker 引擎

> [Docker Cli 官方文档](https://docs.docker.com/engine/reference/commandline/docker/)
{.is-success}

![docker-cli.png](/assets/docker/docker-cli.png)

# 2. Docker 镜像

Linux Docker 主机本地镜像仓库位于 `/var/lib/docker/<storage-driver>`

## 2.1 列出本地主机上的镜像信息

```shell
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

- `-a`：列出本地所有的镜像（含中间映像层）
- `-q` ：只显示镜像ID
- `-digests` ： 显示镜像的摘要信息
- `-no-trunc`：显示完整的镜像信息

帮助命令：

```shell
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
  
$ docker image ls --filter dangling=true
# 返回悬虚的镜像，即 TAG=none 的镜像

$ docker image ls --filter=reference="*:latest"
# 返回标签 latest 的示例

$ docker image ls --format "{{ .Size }}"
5.61MB
156MB
72.9MB

$ docker image ls --format "{{ .Tag }}:{{ .Size }}"
latest:5.61MB
latest:156MB
latest:72.9MB
```

## 2.2 搜索镜像

查询源来自 `http://hub.docker.com`

```shell
$ sudo docker search mysql　# 镜像名

NAME              DESCRIPTION                  STARS      OFFICIAL    AUTOMATED
mysql             MySQL is a widely used,....  9682         [OK]
mariadb           MariaDB is a community.....  3523         [OK]
............

$ docker search alpine --filter "is-official=true"
# 只返回官方仓库

$ docker search alpine --filter "is-automated=true"
# 只显示自动创建的仓库
```

- 搜索`stars`超过 20 的，通过列名过滤

```shell
$ sudo docker search mysql --filter=stars=20
```

- 最多搜索10条(默认25条)

```shell
$ sudo docker search mysql --limit 10
```

## 2.3 拉取镜像

```shell
$ docker pull <repository>:<tag>

$ docker pull mongo:3.3.11
# 从官方 Mongo 库拉取标签为 3.3.11 的镜像

$ docker pull redis:latest
# 从官方 redis 库拉取标签为 latest 的镜像

$ docker pull alpine
# 从官方 alpine 库拉取标签为 latest 的镜像

$ docker pull ydongy/ubuntu
# 从 ydongy 账户为命名空间的 ubuntu 库中拉取标签 latest 的镜像

$ docker pull daocloud.io/library/rabbitmq
# 从第三方镜像仓库服务获取镜像
```

## 2.4 删除镜像

```shell
$ docker rmi 镜像ID[:TAG]
```

- 强制删除 `-f`

```shell
$ sudo docker rmi -f bf756fb1ae65
Untagged: hello-world:latest
Untagged:helloworld@sha256:d58e752213a51785838f9eed2b7a498ffa1cb3aa7f946dda11af39286c3db9a9
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```

- 删除多个

```shell
$ docker rmi -f 镜像名1 镜像名2
```

- 删除全部

```shell
$ docker rmi -f $(docker images -qa)
```

# 3. Docker 容器

## 3.1 新建并启动容器

- **options:**
    - `-name="容器新名字"`: 为容器指定一个名称；
    - `-d`: 后台运行容器，并返回容器ID，也即启动**守护式容器**；
    - `-i`：**以交互模式运行容器，通常与 -t 同时使用**；
    - `-t`：**为容器重新分配一个伪输入终端，通常与 -i 同时使用**；
    - `-e`：环境变量
    - `-P`: 随机端口映射，EXPOSE
    - `-p`: 指定端口映射，有以下四种格式
        - `ip:hostPort:containerPort`
        - `ip::containerPort`
        - `hostPort:containerPort`
        - `containerPort`

```shell
$ docker run -it 镜像ID
```

## 3.2 列出当前所有正在运行的容器

- **options:**
    - `-a` ：列出当前所有正在运行的容器+历史上运行过的
    - `-l` ：显示最近创建的容器。
    - `-n 10`：显示最近创建的10个容器。
    - `-q`：静默模式，只显示容器编号。
    - `-no-trunc`：不截断输出。

```shell
$ sudo docker ps
CONTAINER ID    IMAGE     COMMAND      CREATED             STATUS         PORTS      NAMES
1219aae1f4a8    ubuntu    "/bin/bash"  42 seconds ago     Up 38 seconds            cool_kilby
```

## 3.3 退出容器

```shell
$ exit  ：容器停止运行退出
$ ctrl+P+Q :容器不停止退出
```

## 3.4 重启容器

```shell
$ docker restart 容器ID
```

## 3.5 停止容器

```shell
$ docker stop 容器ID
```

## 3.6 强制停止

```shell
$ docker kill 容器ID
```

## 3.7 删除已停止的容器

```shell
docker rm 容器ID
docker rm $(docker ps -a -q)  #删除多个容器
docker ps -a -q | xrags docker rm #删除多个容器
```

## 3.8 查看容器日志

- **options**
    - `-t` ：加入时间戳
    - `-f`： 跟随最新的日志打印
    - `--tail 30`：显示最后30条

```shell
$ docker logs -f -t --tail 3 容器ID
```

## 3.9 查看容器内运行的进程

```shell
$ docker top 容器ID
```

## 3.10 查看容器内部细节

```
$ docker inspect 容器ID
$ sudo docker inspect --format='{{ .State.Running }}' b4c   
true
$ sudo docker inspect --format='{{ .NetworkSettings.IPAddress }}' b4c
172.17.0.2
```

## 3.11 进入正在运行的容器并以命令行交互

```shell
$ docker exec -it 容器ID /bin/bash
$ docker attach 容器ID
```

- `attach`：直接进入容器启动命令的终端，不会启动新的进程
- `exec`：可以直接在宿主机执行容器内的程序，例如以下：在宿主机查看`ubuntu`容器根目录的文件

```shell
$ sudo docker exec -t 71b ls -l /
total 48
lrwxrwxrwx   1 root root    7 Jun  6 01:18 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Apr 15 11:09 boot
drwxr-xr-x   5 root root  360 Jun 27 10:28 dev
drwxr-xr-x   1 root root 4096 Jun 27 10:28 etc
drwxr-xr-x   2 root root 4096 Apr 15 11:09 home
......
```

## 3.12 容器数据复制

```shell
# 宿主机 file.txt 拷贝到容器的 /root/host.txt
$ sudo docker cp file.txt 容器ID:/root/host.txt

# 容器的 /root/host.txt 拷贝到宿主机当前目录
$ sudo docker cp 容器ID:/root/host.txt .
```

## 3.13 镜像 Commit

类似于 git 的 commit，假如我们的某个镜像启动后，在产生的容器中进行了一些配置的修改，而我们想要保存本次配置过得容器就可以利用它为模板 commit 一个配置过后的镜像，以后启动当前镜像就已经是配置过后的容器

```shell
$ docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]

# eg:commit一个安装过 vim 的 ubuntu 镜像，名称为：atguigu/ubuntu，标签 TAG 为：1.2
$ sudo docker commit -m="install vim" -a="ydongy" 52ca02c8b0c9 atguigu/ubuntu:1.2
```

## 3.14 镜像和容器的导入导出

- 把配置后的镜像打包成压缩文件

```shell
$ docker save -o xxx.tar images_name:tag

# eg:docker save -o my_mysql.tar my_mysql:1.0
```

- 从压缩文件还原镜像

```shell
$ docker load -i xxx.tar
```

- export 和 import

```shell
$ sudo docker export 容器ID > data.tar # 导出
$ sudo docker import - 镜像名 < data.tar # 导入
```

> 这两种方法非常相似。不同的是，保存一个镜像会保留它的历史，而导出容器将对它的历史进行压缩。

# 4. Docker 容器卷

## 4.1 添加数据卷

```shell
$ docker run -it -v /宿主机绝对路经目录:/容器内目录　镜像名

# eg:宿主机的/data/docker-ubuntu目录与容器ubuntu的/MyData目录建立数据共享
$ sudo docker run -it -v /data/docker-ubuntu:/MyData ubuntu

# eg:只读权限，只需要加一个ro，代表read-only
$ sudo docker run -it -v /data/docker-ubuntu:/MyData:ro ubuntu

# 如果存在写权限有问题，可加后缀：--privileged=true
```

## 4.2 DockerFile 添加

```dockerfile
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

## 4.3 数据卷容器

命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器。一句话：继承关系，一个容器继承另一个容器，挂载数据卷可以实现共享

- 启动一个容器

```bash
$ docker run -it --name father ubuntu
```

- 启动一个子容器

```bash
$ docker run -it --name son --volumes-from father ubuntu
```

两个容器对数据卷进行修改时，可以实现共享数据。如果存在多个子容器继承一个父容器，所以的容器都可以共享数据，哪怕把父容器给删除了，剩余的子容器之间仍然能够共享。

# 5. Docker Registry

## 5.1 Docker Hub

- 登录 Docker Hub

```shell
$ sudo docker login
```

- 通过 commit 构建一个新镜像，也可以使用 Dockerfile 构建

```shell
$ sudo docker commit -m="install vim" -a="ydongy" c4f50fc80cc3 ydongy/ubuntu:1.2
```

- 将镜像推送到 Docker Hub

```shell
$ sudo docker push ydongy/ubuntu:1.2
```

## 5.2 私有仓库搭建

```shell
# 1、拉取私有仓库镜像 
$ docker pull registry

# 2、启动私有仓库容器 
$ docker run -id --name=registry -p 5000:5000 registry

# 3、打开浏览器 输入地址http://私有仓库服务器ip:5000/v2/_catalog，看到
{"repositories":[]} # 表示私有仓库 搭建成功

# 4、修改daemon.json   
vim /etc/docker/daemon.json    

# 在上述文件中添加一个key，保存退出。此步用于让 docker 信任私有仓库地址；注意将私有仓库服务器 ip 修改为自己私有仓库服务器真实 ip 
{"insecure-registries":["私有仓库服务器ip:5000"]}

## 5、修改 docker.service
# vim /lib/systemd/system/docker.service
# 在 [service] 选项下添加 EnvionmentFile=/etc/docker/daemon.json

# 6、重启docker 服务 
sudo service docker restart
```

## 5.3 将镜像上传至私有仓库

```shell
# 1、标记镜像为私有仓库的镜像     
docker tag xxx 私有仓库服务器IP:5000/xxx
 
# 2、上传标记的镜像     
docker push 私有仓库服务器IP:5000/xxx
```

## 5.4 从私有仓库拉取镜像 

```shell
# 拉取镜像 
docker pull 私有仓库服务器ip:5000/xxx
```

# 6. 容器资源限制

## memeory

```shell

```





