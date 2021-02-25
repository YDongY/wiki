---
title: Nginx Command
description: 
published: true
date: 2021-02-21T14:40:21.880Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:40:19.308Z
---

# Nginx 命令行

| 参数      | 说明                                                                                                                                             |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `-? 、-h` | 帮助文档                                                                                                                                         |
| `-c`      | 指定使用配置文件                                                                                                                                 |
| `-g`      | 指定配置指令                                                                                                                                     |
| `-p`      | 制定运行目录                                                                                                                                     |
| `-s`      | 发送信号<br>`nginx -s stop`：立即停止服务<br>`nginx -s quit`：优雅退出<br>`nginx -s reload`：重载配置文件<br>`nginx -s reopen`：重新开始记录日志 |
| `-t、-T`  | 检查配置文件语法是否错误                                                                                                                         |
| `-v、-V`  | 打印版本信息、编译信息                                                                                                                           |

# 重载配置文件

修改配置文件，完成以后执行

```shell
$ nginx -s reload
```

# 热部署

在 Nginx 运行状态下更换版本

```shell
$ ps -ef | grep nginx
```

更新步骤：

- 下载新版本 Nginx
- 备份旧版本 nginx 二进制文件
- 把新版本 nginx 二进制文件拷贝到旧版本二进制文件所在目录
- 给正在运行的 master 进程发送信号

```shell
$ kill -USR2 xxx[pid]
```

- 向老 master 进程发送信号

```shell
$ kill -WINCH xxx[pid]
```

以上步骤完成之后，老的 master 进程不会退出，可以对老版本进行回退

# 日志切割

备份需要切割的日志，接着执行命令

```shell
$ nginx -s reopen
```

# Nginx 配置静态服务器

```shell
gzip  on; # 打开压缩
gzip_min_length 1;
gzip_comp_level 2;
gzip_types text/plan application/x-javascript text/css application/xml text/javascript image/jpeg image/gif image/png;

server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            alias  /opt/flask-doc/; # 配置　location 到静态目录
            #root   html;
            #index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```

- 配置目录索引

```shell
location / {
            alias  /opt/flask-doc/; # 配置　location 到静态目录
            autoindex  on;
            #root   html;
            #index  index.html index.htm;
        }
```

访问`/opt/flask-doc/`目录下子文件夹效果：

<div style="text-align: center">
	<img src="/downloads/nginx/nginx.png" class="rounded shadow"/>
</div>

# Nginx 配置反向代理

```shell
upstream local {
    server 127.0.0.1:8080
}

server {
        listen       80;
        server_name  localhost;

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://local; # 代理到上游服务
        }
    }
```