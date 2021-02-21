---
title: 阿里云自动化部署 Hugo 博客
description: 
published: true
<<<<<<< HEAD
date: 2021-02-21T13:34:36.374Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:31.509Z
=======
date: 2021-02-21T14:38:20.139Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:38:17.461Z
>>>>>>> docs: add all untracked content
---

# 1. Webhook 部署

## 1.1 配置 Hugo 自动发布

### 仓库初始化

首先把博客仓库 Clone 下来，并执行 `Hugo` 生成 HTML 文件，如果没有安装 [Hugo](https://gohugo.io/getting-started/installing/#binary-cross-platform) 可自行安装，具体步骤如下：

```shell
mkdir -p /var/www
cd /var/www
git clone git@github.com:YDongY/ydongy.github.io.git
cd ydongy.github.io
# 执行 hugo，默认会生成 public 的目录，里面存放需要 HTML 文件，为以后 Nginx 代理静态资源使用
hugo
```

### 创建自动拉取脚本

为了避免以后手动 `git pull` 和 `hugo` 生成静态资源，我们创建一个名为 `deploy.sh` 的 Shell 脚本，用来编译 Hugo 生成 HTML 文件

```shell
mkdir -p /var/www/webhook
cd /var/www/webhook
vim deploy.sh
chmod +x deploy.sh
```

文件内容：

```bash
#!/bin/bash
# 网站的代码仓库目录
input="/var/www/ydongy.github.io"
cd $input
git pull
hugo
```

### GitHub Webhook 配置

使用 webhook 的目的就是当我们本地修改博客 `push` 到仓库时，可以触发我们服务器的脚本，首先去 `Github` 新建一个 `webhook`

![webhook.png](/tools/阿里云自动化部署hugo博客/webhook.png)

此处创建完毕之后，记住自己设置的 `Secret`，接下来需要使用

### 创建 webhook 服务后端

使用 NodeJS 创建 webhook 服务后端，后端代码保存在 `webhook.js` 文件中，调用 `deploy.sh` 来发布，因此需要与 deploy.sh 文件在同一级目录中，监听 `http://127.0.0.1:6666/webhook`【**监听地址与 Nginx 有关，后面会说**】：

`webhook.js` 文件内容如下：

```js
var http = require('http');
var spawn = require('child_process').spawn;
var createHandler = require('github-webhook-handler');
var handler = createHandler({ path: '/webhook', secret: 'yourwebhooksecret' }); // 将 secret 修改你自己的

http.createServer(function (req, res) {
  handler(req, res, function (err) {
    res.statusCode = 404;
    res.end('no such location');
  })
}).listen(6666); // 监听端口，可以自己修改

handler.on('error', function (err) {
  console.error('Error:', err.message)
});

handler.on('push', function (event) {
  console.log('Received a push event for %s to %s',
    event.payload.repository.name,
    event.payload.ref);

  runCommand('bash', ['/var/www/webhook/deploy.sh'], function( txt ){ // 注意路径，最好写绝对路径
    console.log(txt);
  });
});

function runCommand( cmd, args, callback ){
    var child = spawn( cmd, args );
    var resp = 'Deploy OK';
    child.stdout.on('data', function( buffer ){ resp += buffer.toString(); });
    child.stdout.on('end', function(){ callback( resp ) });
}
```

有了 `webhook.js` 还不行，它还需要安装一个依赖包：

```shell
cd /var/www/webhook
npm i -S github-webhook-handler
```

### PM2 管理 webhook 服务后端

目前为止 `webhook.js` 和 `deploy.sh` 就可以利用 `webhook.js` 监听【**实际是 Nginx 转发给 `webhook.js`**】 Github 的 webhook，从而触发 `deploy.sh` 脚本完成更新博客操作。但是由于 `webhook.js` 需要时刻监听，故需要把它当做守护进程，这里推荐一个管理后台进程的工具 [pm2](https://getpm2.com/)，如果有其他方式也可以。接下来通过 `npm` 进行安装

```shell
npm i -g pm2
```

启动服务

```shell
pm2 start webhook.js
```

查看服务状态

```shell
pm2 list
┌─────┬────────────┬─────────────┬─────────┬─────────┬──────────┬────────┬──────┬───────────┬──────────┬──────────┬──────────┬──────────┐
│ id  │ name       │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼────────────┼─────────────┼─────────┼─────────┼──────────┼────────┼──────┼───────────┼──────────┼──────────┼──────────┼──────────┤
│ 1   │ webhook    │ default     │ N/A     │ fork    │ 1236     │ 77m    │ 4    │ online    │ 0%       │ 42.8mb   │ root     │ disabled │
└─────┴────────────┴─────────────┴─────────┴─────────┴──────────┴────────┴──────┴───────────┴──────────┴──────────┴──────────┴──────────┘
```

## 1.2 安装 Nginx

准备工作完成，下面开始配置 Nginx，使用 Nginx 来部署静态资源，同时转发 Github 发送的 webhook 请求给 `webhook.js`。如果还没有安装 Nginx 可以[点击查看安装](2020-12-16-ubuntu安装nginx/#2-nginx-编译安装官网httpsnginxorgendownloadhtml)先进行安装【**推荐编译安装**】，唯一需要注意的地方就是在编译过程替换成如下命令：

```shell
cd /opt/nginx-1.18.0

# 下面的参数表示需要用到 https http2 模块
./configure --prefix=/usr/local/nginx \
--with-http_gzip_static_module \
--with-http_v2_module \
--with-pcre \
--with-http_ssl_module

make

make install

# 建立软连接
sudo ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
```

### 修改配置

```shell
vim /usr/local/nginx/nginx.conf
```

```conf
server {
    listen                  80;
    server_name             example.com example.com;

    location / {
        root /var/www/ydongy.github.io/public; # 自己的静态博客目录
        index index.html;
    }

    # nginx 转发 webhook 的请求，给 webhook.js ,注意端口和 js 监听端口一致
    location /webhook {
        proxy_pass http://127.0.0.1:6666;
    }
}
```

> 目前为止，Nginx 自动部署 Hugo 博客已经完成，如果需要使用 `https` 访问你的站点，可继续往下看。

## 1.3 申请 SSL 证书

这里推荐 [Let’s Encrypt](https://letsencrypt.org/zh-cn/) 机构，然后使用 [acme.sh](https://github.com/acmesh-official/acme.sh) 从 letsencrypt 生成免费的证书，且可以生成泛域名证书。

参考 [acme.sh 中文 wiki](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E) 、[使用 acme.sh 部署 Let's Encrypt 通过阿里云 DNS 验证方式实现泛域名 HTTPS](https://f-e-d.club/topic/use-acme-sh-deployment-let-s-encrypt-by-ali-cloud-dns-generic-domain-https-authentication.article)

按照上述博客的申请方式最终会执行下面这条命令：

```shell
acme.sh --issue --dns dns_ali -d example.com -d www.example.com\
--installcert\
--key-file /usr/local/nginx/conf/ssl/example.com.key\
--fullchain-file /usr/local/nginx/conf/ssl/example.com.pem\
--reloadcmd "nginx -s reload"
```

- `example.com`：替换成自己的域名
- `--key-file`：后面的参数表示把 `~/.acme.sh/example.com/example.com.key` 拷贝到指定目录文件，【这里的目录可以自己指定，最终会在 Nginx 中配置】
- `--fullchain-file`：同上，只是把 `~/.acme.sh/example.com/fullchain.cer` 拷贝到指定目录文件

修改 Nginx 配置文件如下：

```conf
server {
    listen  80;
    server_name     example.com www.example.com;

    return  301 https://$server_name$request_uri;
}
server {
    listen                  443 ssl;
    server_name             example.com example.com;
    ssl_certificate         ssl/example.com.pem; # 证书路径配置
    ssl_certificate_key     ssl/example.com.key; # 证书路径配置

    location / {
        root /var/www/ydongy.github.io/public; # 自己的静态博客目录
        index index.html;
    }

    # nginx 转发 webhook 的请求，给 webhook.js ,注意端口和 js 监听端口一致
    location /webhook {
        proxy_pass http://127.0.0.1:6666;
    }
}
```

# 2. Github Action 部署

由于之前采用 WebHook 部署，其目的是在 push 的时候触发 webhook 给服务器发送请求，从而在服务器执行 git pull 等一系列构建操作。但是由于阿里云的服务器使用 git pull 异常的慢。所以决定采用 Github Action 的方式，利用 ssh 把构建好的静态页面直接通过 rsync 方式同步到服务器。

## 2.1 建立 Actions

![actions.png](/tools/阿里云自动化部署hugo博客/actions.png)

然后选择一个合适的 ssh 工具，不一定需要 star 多的，最好适合自己的使用场景，保证简单、传输速度高效即可。接着就是配置 `yml` 文件：

```yml
name: Deploy Blog To Aliyun

on:
  push:
    branches:
      - master  # 只在 master 上 push 触发部署
    paths-ignore:   # 下列文件的变更不触发部署，可以自行添加
      - README.md
      - LICENSE

jobs:

  deploy:
    runs-on: ubuntu-latest   # 使用 ubuntu 系统镜像运行自动化脚本

    steps: # 自动化步骤
      - uses: actions/checkout@v2 # 第一步，下载代码仓库

    
      - name: Setup Hugo # 第二步，安装 hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.79.0'
          extended: true # 使用 hugo 扩展版本，避免无法使用 hugo 的 Shortcodes

      - name: Build  # 第三步，编译 hugo
        run: |
          cd .
          hugo -D --minify
      
      - name: Deploy
        uses: AEnterprise/rsync-deploy@v1.0 # ssh 工具 (https://github.com/AEnterprise/rsync-deploy)
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }} # 阿里云的私钥 (~/.ssh/id_rsa)
          ARGS: "-avz --delete" # 可以添加参数忽略某些文件
          SERVER_PORT: ${{ secrets.DEPLOY_PORT }} # 阿里云 ssh 端口 (22)
          FOLDER: "./public/*" # 希望上传的目录，根目录为仓库
          SERVER_IP: ${{ secrets.DEPLOY_HOST }} # 阿里云的 IP 或 域名
          USERNAME: ${{ secrets.DEPLOY_USER }} # 阿里云登录用户名
          SERVER_DESTINATION: "/var/www/notes/Blog" # 希望上传到阿里云的目标地址
```

更多的 Action 参数配置可查看[官方文档](https://docs.github.com/cn/free-pro-team@latest/actions)或者[GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)，工具的详情配置可查看对应工具的 README

## 2.2 配置私钥

这一步坑贼多，配置不好 ssh 就无法连接服务器。首先在阿里云服务器生成公私钥：

```shell
ssh-keygen -t rsa -C "邮箱地址"
```

然后把 `~/.ssh/id_rsa.pub` 的内容复制到 `~/.ssh/authorized_keys` 文件中

```shell
cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys
```

然后修改阿里云 `/etc/ssh/sshd_config` ，添加如下内容：

```shell
PermitRootLogin yes
PubkeyAuthentication yes
PasswordAuthentication yes
```

最后重启 sshd 服务：

```shell
service sshd restart
```

### 2.3 配置 Github Secrets

配置完成公私钥之后，接下来及时配置 Github Secrets，因为我们在 `yml` 文件中进行了引用，总不能直接把私钥地址明文丢在配置文件里吧🤣。进行配置可在项目目录下点击 `settings->secrets`。

![secrets.png](/tools/阿里云自动化部署hugo博客/secrets.png)

注意命名要与 `yml` 配置文件一致：

```yml
DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }} # 阿里云的私钥 (~/.ssh/id_rsa)
ARGS: "-avz --delete" # 可以添加参数忽略某些文件
SERVER_PORT: ${{ secrets.DEPLOY_PORT }} # 阿里云 ssh 端口 (22)
FOLDER: "./public/*" # 希望上传的目录，根目录为仓库
SERVER_IP: ${{ secrets.DEPLOY_HOST }} # 阿里云的 IP 或 域名
USERNAME: ${{ secrets.DEPLOY_USER }} # 阿里云登录用户名
SERVER_DESTINATION: "/var/www/notes/Blog" # 希望上传到阿里云的目标地址
```

完成之后，可以通过对自己设置的分支进行 push 操作，即可在 Actions 页面查看时候发布过程的详细信息。

# 参考

- [nginx 配置 https](https://juejin.cn/post/6844904063688179720#heading-4)
- [使用 GitHub Webhook 实现静态网站自动化部署](https://jimmysong.io/blog/github-webhook-website-auto-deploy/#github-webhook-%E9%85%8D%E7%BD%AE)
- [acme.sh 中文 wiki](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)
- [使用 acme.sh 部署 Let's Encrypt 通过阿里云 DNS 验证方式实现泛域名 HTTPS](https://f-e-d.club/topic/use-acme-sh-deployment-let-s-encrypt-by-ali-cloud-dns-generic-domain-https-authentication.article)
- [Github Actions 官方文档](https://docs.github.com/cn/free-pro-team@latest/actions)
- [GitHub Actions 入门教程](http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html)