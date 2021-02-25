---
title: 阿里云使用 Clash 代理
description: 
published: true
date: 2021-02-21T14:37:15.249Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:37:12.673Z
---

由于本人的 ECS 是国内的，想要访问 Github 真的是太慢了，于是就想用一下机场的代理。【早知道，就应该买香港的服务器😭】

## 1. 下载 Clash

从 [Github](https://github.com/Dreamacro/clash/releases/tag/v1.3.0) 下载 `clash` 的 linux-amd64 可执行文件。

```shell
gzip -d clash-linux-amd64-v1.3.0.gz
chmod +x clash-linux-amd64-v1.3.0
sudo cp clash-linux-amd64-v1.3.0 /usr/local/bin/clash
```

## 2. 下载 Country.mmdb

从 [Github](https://github.com/Dreamacro/maxmind-geoip/releases/tag/20201212) 下载，然后在 `~/.config` 目录下创建 `clash` 文件夹，里面放入 `Country.mmdb` 文件和自己机场的 `config.yaml` 文件

```shell
mkdir -p ~/.config/clash
mv Country.mmdb ~/.config/clash/
```

之后直接输入命令 `clash` 启动查看效果

```shell
$ sudo clash
INFO[0000] Start initial compatible provider 故障转移
INFO[0000] Start initial compatible provider 自动选择
INFO[0000] Start initial compatible provider 大机场 Big Airport
INFO[0000] RESTful API listening at: 127.0.0.1:9090
INFO[0000] HTTP proxy listening at: 127.0.0.1:7890
INFO[0000] SOCKS proxy listening at: 127.0.0.1:7891
```

## 3. 配置环境变量启用代理

如果配置文件正确，下面就只需要修改环境变量，使用代理端口转发流量

```shell
export https_proxy=http://127.0.0.1:7890
export http_proxy=http://127.0.0.1:7890
export all_proxy=socks5://127.0.0.1:7890
```

如果想要永久生效，只需要将上面的代码添加带 `~/.bashrc` 中，【**此处可以不设置，后面会有函数设置**】

```shell
vim ~/.bashrc
source ~/.bashrc
```

## 4. 配置 GUI 界面

虽然我们是在终端启动 `clash` ，但是 `clash` 启动完成之后有一个 `RESTful API` ，可以让我们通过浏览器访问，我们需要下载一个 [yacd](https://github.com/haishanh/yacd/releases/tag/v0.2.13)

```shell
wget https://github.com/haishanh/yacd/releases/download/v0.2.13/yacd.tar.xz
tar -xf yacd.tar.xz
mv public/ ~/.config/clash/dashboard
```

接着在 `config.yaml` 中如下设置：

```yaml
# 如果使用阿里云设置如下，需要需要注意自己 ECS 需要开启安全组端口访问
external-controller: '0.0.0.0:9090'
# 如果是本地设置如下
external-controller: '127.0.0.1:9090'
external-ui: 'dashboard'
secret: 'test'
```

启动 `clash` 后，浏览器使用 `ip:port/ui` 的方式访问，如下所示:

![dashboard.png](/tools/阿里云使用clash代理/dashboard.png)

首次启动可能需要添加 API URL，如果是本地可以直接访问 `http://127.0.0.1:9090`，如果是 ECS 需要输入对应的公网地址 `http://ip:9090` ，`secret` 就是之前在 `config.yaml` 配置的。

## 5. 后台启用代理

先将 `clash` 作为一个 `daemon` 进程，[点击参考](https://github.com/Dreamacro/clash/wiki/clash-as-a-daemon)，这里使用 [pm2](https://getpm2.com/)

```shell
$ sudo npm install pm2@latest -g
$ ln -s /opt/node-v14.15.1-linux-x64/lib/node_modules/pm2/bin/pm2 /usr/local/bin/pm2
$ pm2 start clash
[PM2] Starting /usr/local/bin/clash in fork_mode (1 instance)
[PM2] Done.
┌────┬────────────────────┬──────────┬──────┬───────────┬──────────┬──────────┐
│ id │ name               │ mode     │ ↺   │ status    │ cpu      │ memory   │
├────┼────────────────────┼──────────┼──────┼───────────┼──────────┼──────────┤
│ 0  │ clash              │ fork     │ 0    │ online    │ 0%       │ 17.2mb   │
└────┴────────────────────┴──────────┴──────┴───────────┴──────────┴──────────┘
```

然后将代理函数写入 `~/.bashrc` 文件中，然后保存 `source ~/.bashrc`

```bash
PROXY_IP=127.0.0.1
PROXY_PORT=7890

function Proxy() {
        if [ "$1" == "on" ]
        then
                export https_proxy=$PROXY_IP:$PROXY_PORT
                export http_proxy=$PROXY_IP:$PROXY_PORT
                echo Proxy On
        elif [ "$1" == "off" ]
        then
                unset https_proxy
                unset http_proxy
                echo Proxy Off
        else
                echo "Use Example:
                Proxy on
                Proxy off"
        fi
}
```

- 启用代理

```shell
Proxy on
```

- 关闭代理

```shell
Proxy off
```
