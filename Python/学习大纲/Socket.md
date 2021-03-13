---
title: 19. Socket
description: 
published: true
date: 2021-03-13T04:57:29.146Z
tags: 
editor: markdown
dateCreated: 2021-03-13T04:39:49.209Z
---

# Python 中的 Socket

下面将使用的主要模块就是 socket 模块，在这个模块中可以找到 socket()函数，该函数用于创建套接字对象。套接字有自己的方法集，这些方法可以实现基于套接字的网络通信，分别创建 TCP 的客户端，服务端和 UDP 协议客户端，服务端。

## 创建 TCP 服务器

socket 创建 TCP 服务器的过程：

1. 创建 TCP 服务器套接字
2. 把服务器地址端口绑定到到套接字
3. 开启 TCP 监听器的调用，因为 TCP  是面向连接的，需要三次握手
4. 等待连接，连接成功之后就是等待客户端发送数据
5. 处理请求，返回响应

```python
import socket

from time import ctime

HOST = '127.0.0.1'
PORT = 8888
BUFFER_SIZE = 1024  # 缓冲区大小设置为 1KB
ADDR = (HOST, PORT)

#  创建TCP 服务器套接字
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server_socket.bind(ADDR)  # 套接字绑定到服务器地址
server_socket.listen(5)  # 开启 TCP 监听器的调用。传入连接请求的最大数。

while True:
    print("waiting for connecting...")
    # 等待客户端的连接
    client_socket, addr = server_socket.accept()
    print("success connected from {}".format(addr))

    while True:
        # 等待客户端发送的消息
        data = client_socket.recv(BUFFER_SIZE)
        if not data:
            break
        # 格式化并返回相同的数据
        client_socket.send(('[{}] {}'.format(ctime(), data)).encode())
    client_socket.close()
server_socket.close()
```

# 创建 TCP 客户端

socket 创建 TC P客户端的过程：

1. 创建 TCP 客户端套接字
2. 连接到指定服务器 IP 和端口
3. 发送数据
4. 接受响应

```python
import socket

from time import ctime

HOST = '127.0.0.1'
PORT = 8888
BUFFER_SIZE = 1024  # 缓冲区大小设置为 1KB
ADDR = (HOST, PORT)

#  创建TCP 客户端套接字
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 连接到服务器
client_socket.connect(ADDR)

while True:
    data = input(">")
    if not data:
        break
    client_socket.send(data.encode())
    data = client_socket.recv(BUFFER_SIZE)
    if not data:
        break
    print(data.decode())

client_socket.close()
```

## 创建 UDP 服务器

socket 创建 UDP 服务器的过程：

1. 创建 UDP 服务器套接字
2. 把服务器地址端口绑定到到套接字
3. 等待客户端发送数据
4. 处理请求，返回响应

```python
import socket

from time import ctime

HOST = '127.0.0.1'
PORT = 8888
BUFFER_SIZE = 1024  # 缓冲区大小设置为 1KB
ADDR = (HOST, PORT)

#  创建UDP 服务器套接字
server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

server_socket.bind(ADDR)  # 套接字绑定到服务器地址

while True:
    print("waiting for connecting...")
    data, addr = server_socket.recvfrom(BUFFER_SIZE)
    # 格式化并返回相同的数据
    server_socket.sendto(('[{}] {}'.format(ctime(), data)).encode(), addr)
    print("...received from and returned to:", data)
server_socket.close()
```


## 创建 UDP 客户端
socket 创建 UDP 客户端的过程：

1. 创建 UDP 客户端套接字
2. 发送数据到指定 IP 和端口服务器
3. 接受响应

```python
import socket

from time import ctime

HOST = '127.0.0.1'
PORT = 8888
BUFFER_SIZE = 1024  # 缓冲区大小设置为 1KB
ADDR = (HOST, PORT)

#  创建UDP 客户端套接字
client_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

while True:
    data = input(">")
    if not data:
        break
    client_socket.sendto(data.encode(), ADDR)
    data = client_socket.recvfrom(BUFFER_SIZE)
    if not data:
        break
    print(data)

client_socket.close()
```

## SocketServer TCP 服务器

下面这个不是重点可以不看，主要是没用过，就随手记录下了。

SocketServer 是标准库中的一个高级模块，它的目标是简化很多样板代码，只需要创建网络客户端和服务器所必需的代码。

```python
from socketserver import TCPServer, StreamRequestHandler
from time import ctime

HOST = '127.0.0.1'
PORT = 8888
BUFFER_SIZE = 1024  # 缓冲区大小设置为 1KB
ADDR = (HOST, PORT)

class RequestHandler(StreamRequestHandler):

    def handle(self) -> None:
        """接收到一个来自客户端的消息时，它就会调用 handle()方法"""
        print("success connected from {}".format(self.client_address))

        # StreamRequestHandler类将输入和输出套接字看作类似文件的对象，因此我们将使用 readline()来获取客户端消息，并利用 write()将字符串发送回客户端。
        self.wfile.write(('[{}] {}'.format(ctime(), self.rfile.readline())).encode())

server = TCPServer(ADDR, RequestHandler)
print("waiting for connecting...")

server.serve_forever()
```

## SocketServer TCP 客户端

```python
import socket
from time import ctime

HOST = '127.0.0.1'
PORT = 8888
BUFFER_SIZE = 1024  # 缓冲区大小设置为 1KB
ADDR = (HOST, PORT)

while True:
    #  创建TCP 客户端套接字
    client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 连接到服务器
    client_socket.connect(ADDR)
    data = input(">")
    if not data:
        break
    client_socket.send('{}\r\n'.format(data).encode())
    data = client_socket.recv(BUFFER_SIZE)
    if not data:
        break
    print(data.strip().decode())
    client_socket.close()
```

> SocketServer 请求处理程序的默认行为是接受连接、获取请求，然后关闭连接。由于这
> 个原因，我们不能在应用程序整个执行过程中都保持连接，因此每次向服务器发送消息时，
> 都需要创建一个新的套接字。
> 
> 因为 StreamRequestHandler 使用的处理程序类对待套接字通信就像文件一样， 所以必须发送行终止符（回车和换行符)
{.is-info}

# 基于 Socket 的 Web 服务器

我们了解到了 HTTP 报文的格式，但是我们需要关注的是服务器，因为Web 应用同样遵循客户端/服务器架构，而此时的客户端就是是浏览器， 服务器端就是 Web 服务器。也就是说我们不太需要去编写客户端，因为我们通过浏览器去访问我们的服务器，浏览器自然会遵循 HTTP 请求报文的格式，而我们的服务器想要有回应就必须遵循 HTTP 响应报文的格式。

## 纯文本响应服务器

下面就是一个简单的 Web 服务器，利用 Socket + 多线程，再此不考虑技术的选型，主要是了解 HTTP 通信的过程：

```python
import socket
import threading

def request_handler(client_socket):
    request_content = client_socket.recv(1024).decode("utf-8")
    print(request_content)
    client_socket.send(b"HTTP/1.1 200 OK\r\n\r\nhello world")　# 响应报文
    client_socket.close() # 关闭连接

def main():
    # 1. 创建tcp套接字
    tcp_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 完成3次握手4次挥手，重复使用端口
    tcp_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    # 2. 绑定
    tcp_socket.bind(("127.0.0.1", 8888))
    # 3. 监听
    tcp_socket.listen(128)
    # 4. 等待链接
    while True:
        print("----服务器已经开启----")
        client_socket, client_addr = tcp_socket.accept()
        # 开启线程
        threading.Thread(target=request_handler, args=(client_socket,)).start()
        # request_handler(client_socket)
    # tcp_socket.close()

if __name__ == '__main__':
    main()
```

代码的执行过程说一下：

1. 创建 TCP 套接字
2. 为套接字绑定IP和端口号
3. 监听客户端连接或浏览器连接
4. 等待客户端发送数据，存在一个客户端连接就创建一个线程，交给 request_handler 处理，然后始终返回 hello world
5. 断开与客户端的连接

> 注意：浏览器请求的过程其实也是需要三次握手和四次挥手，因为他们传输层是 TCP。
{.is-warning}

![socket-1.png](/python/学习大纲/网络编程/socket-1.png)

![socket-2.png](/python/学习大纲/网络编程/socket-2.png)

![socket-3.png](/python/学习大纲/网络编程/socket-3.png)

## 伪静态响应服务器

目前我们实现的是无论什么 HTTP 请求，返回的总是 hello world，这必然不是我们想要的。那么如果想要实现传输 html 页面的数据，那就可以把 request_handler 里面的逻辑更改，比如下面的这部分代码：

```python
def response_content(title):
    if title == "/":
        title = "/index.html"
    try:
        with open(title[1:], 'rb') as f: # 读取页面内容
            content = f.read()
    except Exception as e:
        with open('404.html', 'rb') as f:　# 异常一律返回404页面
            content = f.read()
    return content

def request_handler(client_socket):
    request_content = client_socket.recv(1024).decode("utf-8")
    print(request_content)
    ret = re.match(r"GET (/.*) HTTP/1.1", request_content) # 匹配请求URL
    if ret:
        title = ret.group(1)
        content = response_content(title)　# 处理
        client_socket.send(b"HTTP/1.1 200 OK\r\n" + b"\r\n" + content)　# 响应报文
    client_socket.close()
```

![socket-4.png](/python/学习大纲/网络编程/socket-4.png)

![socket-5.png](/python/学习大纲/网络编程/socket-5.png)

通过两个案例应该了解了 HTTP 协议请求响应的过程，我们其实就明白了一个 Web 应用的本质就是：

1. 浏览器发送一个 HTTP 请求；
2. 服务器收到请求，生成一个 HTML 文档；
3. 服务器遵循 HTTP协议格式，组织 HTML 文档作为 HTTP 响应的 Body 发送给浏览器；
4. 浏览器收到 HTTP 响应，从 HTTP Body 取出 HTML 文档并显示。


# 符合 WSGI 的 Web 服务器

上面我们应该清楚了，只要我们的服务器遵循 HTTP 协议，并且按照 HTTP 协议格式返回数据就可以给客户端返回响应，但是有个问题就是，我们处理数据的逻辑以及组织 HTTP 响应报文的逻辑揉在了一起，如果是动态请求，那么我们就需要不断的组织 HTTP 响应报文，代码越来乱，耦合性越来越高。

于是就出现了 WSGI，需要清楚的是 WSGI 不是服务器，也不是用于与程序交互的 API，更不是真实的代码，而只是定义的一个接口。目标是在 Web 服务器和 Web 框架层之间提供一个通用的 API 标准，减少之间的互操作性并形成统一的调用方式。

下面一张图是符合 WSGI 标准的请求流程：

![socket-6.png](/python/学习大纲/网络编程/socket-6.png)

接下来我们来看一下 WSGI 的定义：

```python
def simple_wsgi_app(environ, start_response):
    status = '200 OK'
    headers = [('Content-type', 'text/html')]
    start_response(status, headers)
    return ['Hello world!']
```

上面的 simple_wsgi_app 函数就是符合 WSGI 标准的一个 HTTP 处理函数，它接收两个参数，返回的内容必须是可迭代的：

- environ：一个包含所有HTTP请求信息的字典对象；
- start_response：一个发送HTTP响应的函数，响应必须含有 HTTP 返回码，以及 HTTP 响应头。

整个 simple_wsgi_app() 函数本身没有涉及到任何解析 HTTP 的部分，也就是说，把底层 web 服务器解析部分和应用程序逻辑部分进行了分离，这样开发者就可以专心做一个领域了。所以simple_wsgi_app() 函数必须由 WSGI 服务器来调用。

- 应用程序

```python
def index():
    return "index page"

def login():
    return "login page"

def application(environ, start_response):
    """
    提供给服务器调用的函数
    :param environ: {"xxx":"xxx"....}
    :param start_response: 服务器函数引用
    :return: 返回响应体内容
    """
    status = '200 OK'
    headers = [('Content-type', 'text/plain;charset=utf-8')]
    start_response(status, headers)
    print(environ)
    func = urlpatterns.get(environ.get("URL"))
    if not func:
        return [""]
    resp = func()
    return [resp]

urlpatterns = {
    "/": index,
    "/index": index,
    "/login": login
}
```

- 服务器

```python
import socket
import threading
import re

class WSGIServer(object):
    def __init__(self, app, ip='127.0.0.1', port=8888, listen=128):
        # 1. 创建tcp套接字
        self.tcp_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        # 完成3次握手4次挥手，重复使用端口
        self.tcp_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        # 2. 绑定
        self.tcp_socket.bind((ip, port))
        # 3. 监听
        self.tcp_socket.listen(listen)

        self.application = app

    def run(self):
        # 4. 等待链接
        while True:
            print("----服务器已经开启 IP:{} Port:{}----".format("127.0.0.1", 8888))
            client_socket, client_addr = self.tcp_socket.accept()
            # 开启线程
            threading.Thread(target=self.request_handler, args=(client_socket,)).start()
            # client_socket.close()

    def request_handler(self, client_socket):
        """
        请求处理函数
        :param client_socket: 客户端Socket
        :return: 返回响应
        """
        request_content = client_socket.recv(1024).decode("utf-8")
        content_list = request_content.split("\r\n")
        url = re.match(r"GET (/.*) HTTP/1.1", content_list[0]).group(1)

        environ = dict()

        environ["URL"] = url

        # 调用应用框架接口
        body = self.application(environ=environ, start_response=self.start_response)

        # 组织响应头
        header = "HTTP/1.1 {status}\r\n".format(status=self.status)
        for temp in self.headers:
            header += "{key}:{value}\r\n".format(key=temp[0], value=temp[1])

        # 合并响应头和响应体
        response_content = header + "\r\n" + "\r\n".join(body)

        # 返回响应
        client_socket.send(response_content.encode())

        # 关闭客户端连接
        client_socket.close()

    def start_response(self, status, headers):
        """
        获取应用程序设置的status,header
        :param status: 200 OK / 404 Not Found
        :param headers:[("server", "my server 1.0"),("xx","xx")....]
        :return:
        """
        self.status = status
        self.headers = [("server", "my server 1.0")]
        self.headers += headers

def main():
    from my_flask import application # 导入应用程序
    # 启动http服务器
    http_server = WSGIServer(app=application)
    # 运行http服务器
    http_server.run()

if __name__ == '__main__':
    main()
```

## WSGI 工具包 Werkzeug

上面的代码基本的实现已经完成了 WSGI 的标准，当然这也太简陋了。俗话说：人生苦短，我用Python，何必重复造轮子，用现成的不香啊！香，真香啊！下面就来说说 Werkzeug：

```python
import os
import redis
from werkzeug.wrappers import Request, Response
from werkzeug.wsgi import SharedDataMiddleware

class Shortly(object):

    def __init__(self, config):
        self.redis = redis.Redis(config['redis_host'], config['redis_port'])

    def dispatch_request(self, request):
        return Response('Hello World!')

    def wsgi_app(self, environ, start_response):
        request = Request(environ)
        response = self.dispatch_request(request)
        return response(environ, start_response)

    def __call__(self, environ, start_response):
        return self.wsgi_app(environ, start_response)

def create_app(redis_host='localhost', redis_port=6379, with_static=True):
    app = Shortly({
        'redis_host': redis_host,
        'redis_port': redis_port
    })
    if with_static:
        app.wsgi_app = SharedDataMiddleware(app.wsgi_app, {
            '/static': os.path.join(os.path.dirname(__file__), 'static')
        })
    return app

if __name__ == '__main__':
    from werkzeug.serving import run_simple

    app = create_app()
    run_simple('127.0.0.1', 5000, app, use_debugger=True, use_reloader=True)
```

程序实现思路：

1. app = create_app() 内部创建了一个对象，返回对象实例
2. 启动一个简单的 HTTP 服务器，接受一个参数 app。调用 run_simple 方法，实际是一个闭包，执行内部的 inner() 方法，服务器最终启动
3. 请求到来，符合 WSGI 标准应该执行 app()，而 app 是一个类，所以执行 `__call__` 方法，开始处理请求，完成之后再把响应返回，再由服务器组织发送给客户端。

## 窥探 Flask 请求流程

1. 启动 Flask 程序

```python
from flask import Flask
app = Flask(__name__)

@app.route("/index/")
def index():
    return "index"

if __name__ == '__main__':
    app.run()　# 同时启动 run_simple(host, port, self, **options)，传入 self = app
```

2. app.run()

```python
def run(self, host=None, port=None, debug=None, load_dotenv=True, **options):
        .....
        from werkzeug.serving import run_simple

        try:
            run_simple(host, port, self, **options)
        finally:
            .....
```

3. run_simple()

```python
def run_simple(hostname,port,application,use_reloader=False,use_debugger=False......):
    .....
    def inner():
        ....
        # 内部有线程，进程，但最终实例化的都是BaseWSGIServer的对象
        srv = make_server(hostname,port,application,......)
        ......
        # 内部HTTPServer.serve_forever(self)，而serve_forever最终实现的是selector.select(0.5)
        srv.serve_forever()
        
    if use_reloader:
        ......
        from ._reloader import run_with_reloader
		
        # 内部启动了一个守护线程，实际还是调用inner
        run_with_reloader(inner, extra_files, reloader_interval, reloader_type)
    else:
        inner()
```

我们可以看出来，Flask 中启动项目，实则是启动了 Werkzeug 提供的服务器，传入了自身的实例对象 app，按照 WSGI 标准在最终接受请求的时候应该执行 app()，而此时 app 是一个对象，所以就会调用 Flask 类中的 `__call__` 方法。开始处理请求，完成之后再把响应返回，再由服务器组织发送给客户端。

OK，现在应该知道Flask有这么一个流程：

1. app.run()
2. 启动服务器等待请求连接
3. 请求执行 `Flask` 中的 `__call__` 方法
4. Flask 应用中的处理（什么钩子函数，上下文，url 匹配，视图函数处理，模板渲染）最终返回响应给 `__call__`
5. 然后再由`__call__`方法返回给 WSGI 服务器
6. 服务器组织响应报文，返回给客户端
7. 断开连接







