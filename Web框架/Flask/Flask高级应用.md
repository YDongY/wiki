---
title: Flask 高级应用
description: 
published: true
date: 2021-03-24T14:49:34.877Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:49:34.877Z
---

# 1. 文件上传

用 Flask 处理文件上传很容易，只要确保不要忘记在你的 HTML 表单中设置 `enctype="multipart/form-data"` 属性就可以了。否则浏览器将不会传送你的文件。

已上传的文件被储存在内存或文件系统的临时位置。你可以通过请求对象 `files` 属性来访问上传的文件，`save()`方法用于把上传文件保存到服务器的文件系统中。

文件上传的基本原理实际上很简单，基本上是：

1. 一个带有 `enctype=multipart/form-data` 的标记，标记中含有一个 。
2. 应用通过请求对象的 `files` 字典来访问文件。
3. 使用文件的 `save()`方法把文件 永久地保存在文件系统中。

**案例：上传文件到一个指定目录**

- 前端代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="http://127.0.0.1:5000/upload/" method="post" enctype="multipart/form-data">
    <input type="file" name="file">
    <input type="submit" value="上传">
</form>
</body>
</html>
```

- 后端代码

```python
import os
from flask import Flask, flash, request, redirect, url_for, render_template
from werkzeug.utils import secure_filename

UPLOAD_FOLDER = os.path.dirname(os.path.abspath(__name__))
ALLOWED_EXTENSIONS = {'txt', 'pdf', 'png', 'jpg', 'jpeg', 'gif'}

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
app.config['SECRET_KEY'] = "ahfkkalahp9qnofnovna"
app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024 # 尺寸限制为 16 M


def allowed_file(filename):
    # 判断后缀
    return '.' in filename and \
           filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS


@app.route("/upload/", methods=["POST", "GET"])
def upload():
    if request.method == "GET":
        return render_template("upload.html")
    else:
        if 'file' not in request.files:　# 判断表单name
            flash('No file part')
            return redirect(request.url)
        file = request.files['file']
        if file.filename == '': # 判断文件名称
            flash('No selected file')
            return redirect(request.url)
        if file and allowed_file(file.filename):　# 判断文件后缀是否符合ALLOWED_EXTENSIONS
            filename = secure_filename(file.filename)
            file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
            return "upload ok"

if __name__ == '__main__':
    app.run(debug=True)
```

- `UPLOAD_FOLDER` ：是上传文 件要储存的目录。
- `ALLOWED_EXTENSIONS` ：是允许上传的文件扩展名的集合。
- `MAX_CONTENT_LENGTH`：限制文件大小
- `SECRET_KEY`：post 请求需要配置该参数
- `secure_filename()` ：假设`filename = "../../../../home/username/.bashrc"`，你会把它和 UPLOAD_FOLDER 结合在一起，那么用户就可能有能力修改一个服务器上的文件，这个文件本来是用户无权修改的

现在来看看函数是如何工作的：

```python
>>> secure_filename('../../../../home/username/.bashrc')
'home_username_.bashrc'
```

> 上传扩展　[Flask-Uploads](https://pythonhosted.org/Flask-Uploads/)
{.is-success}


# 2. 会话

## 2.1 Cookies

要访问 cookies ，可以使用 `cookies` 属性。可以使用响应 对象 的 `set_cookie` 方法来设置 cookies 。请求对象的 `cookies` 属性是一个包含了客户端传输的所有 cookies 的字典。

- 读取 cookies

```python
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
```

- 储存 cookies

```python
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

注意， cookies 设置在响应对象上。通常只是从视图函数返回字符串， Flask 会把它们转换为响应对象。如果你想显式地转换，那么可以使用 `make_response()` 函数，然后再修改它。

```python
set_cookie(key，value =''，max_age = None，expires = None，path ='/'，domain = None，secure = False，httponly = False，samesite = None)
```

|    参数    |                                            介绍                                             |
| :--------: | :-----------------------------------------------------------------------------------------: |
|   `key`    |                                             键                                              |
|  `value`   |                                             值                                              |
| `max_age`  |                                      过期时间，单位秒                                       |
| `expries`  | 过期时间，`datetime`类型，这个时间需要设置为格林尼治时间，也就是要距离北京少8个小时的时间。 |
|   `path`   |                                    表示存储浏览器根目录                                     |
|  `domain`  |                                          设置跨域                                           |
|  `secure`  |                                  为`True`表示仅支持`HTTPS`                                  |
| `httponly` |     禁止`JavaScript`访问`Cookie`。 这是个`Cookie`标准的扩展，可能不是所有浏览器都支持。     |
| `samesite` |                         限制`cookie`的范围，只能访问当前设置的站点                          |

- 删除 cookies

```python
   # 源码
    def delete_cookie(self, key, path="/", domain=None):
        """Delete a cookie.  Fails silently if key doesn't exist.

        :param key: the key (name) of the cookie to be deleted.
        :param path: if the cookie that should be deleted was limited to a
                     path, the path has to be defined here.
        :param domain: if the cookie that should be deleted was limited to a
                       domain, that domain has to be defined here.
        """
        self.set_cookie(key, expires=0, max_age=0, path=path, domain=domain)
```

## 2.2 Session

除了请求对象之外还有一种称为 `session` 的对象，允许你在不同请求之间储存信息。这个对象相当于用密钥签名加密的 cookie ，即用户可以查看你的 cookie ，但是如果没有密钥就无法修改它。

**案例：**

```python
import os
from flask import session, Flask

app = Flask(__name__)
app.config['SECRET_KEY'] = os.urandom(16)

@app.route("/set-session/")
def set_session():
    session["username"] = "jack"
    return "set session ok"

@app.route("/get-session/")
def get_session():
    username = session.get("username")
    if not username:
        return "get session fail"
    else:
        return username

if __name__ == '__main__':
    app.run(debug=True)
```

- 设置 Session

```python
session[key] = value
```

- 获取 Session

```python
session[key]
session.get(key)
```

- 删除 Session某个键值

```python
session.pop(key)
del session[key]
session.clear() # 清空
```

> Session 的设置、获取、删除类似于字典的操作
{.is-info}

- 过期时间

```python
session.permanent=True
```

> 如果设置为“ True”，则会话的生存时间为[`permanent_session_lifetime`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.Flask.permanent_session_lifetime)秒。 默认值为31天。 如果设置为False（默认设置），则当用户关闭浏览器时，会话将被删除。
{.is-info}


`PERMANENT_SESSION_LIFETIME` 配置过期时间，单位：秒

# 3. 信号

信号(`Signal`)就是两个独立的模块用来传递消息的方式，它有一个消息的发送者`Sender`，还有一个消息的订阅者`Subscriber`。

`Flask`的信号功能是基于[Blinker](https://pypi.python.org/pypi/blinker)的，在开始此篇之前，你先要安装 blinker 包`pip install blinker`。

## 3.1 创建信号

定义信号需要使用到blinker这个包的 [`Namespace`](http://discorporate.us/projects/Blinker/docs/1.1/api.html#blinker.base.Namespace) 类来创建一个命名空间。

```python
from blinker import Namespace
my_signals = Namespace()
login_signal = mySpace.signal('创建一个登录信号')
```

## 3.2 订阅信号

使用信号的 [`connect()`](http://discorporate.us/projects/Blinker/docs/1.1/api.html#blinker.base.Signal.connect) 方法来订阅信号。该函数的第一个参数是信号发出时要调用的函数，第二个参数是可选的，用于确定信号的发送端。退订一个信号，可以使用[`disconnect()`](http://discorporate.us/projects/Blinker/docs/1.1/api.html#blinker.base.Signal.disconnect) 方法。

```python
login_signal.connect(login_log)
```

## 3.3 发送信号

发出信号，调用 [`send()`](http://discorporate.us/projects/Blinker/docs/1.1/api.html#blinker.base.Signal.send) 方法可以做到。 它接受发送端作为第一个参数，和一些推送到信号订阅者的可选关键字参数

```python
login_signal.send()
```

## 3.4 信号使用场景

- 定义一个登录的信号，以后用户登录进来
- 发送一个登录信号，然后能够监听这个信号
- 在监听到这个信号以后，就记录当前这个用户登录的信息
- 用信号的方式，记录用户的登录信息即登录日志

１．**编写一个`signal.py`文件创建登录信号**

```python
from blinker import Namespace
from datetime import datetime
from flask import request, g

# 创建一个登录信号
mySpace = Namespace()
login_signal = mySpace.signal('创建一个登录信号')

# 监听信号
def login_log(sender):
    # 用户名   时间 ip
    username = g.username
    now = datetime.now()
    ip = request.remote_addr
    log_data = '{username}*{now}*{ip}'.format(username=username, now=now, ip=ip)
    with open('login_log.txt', 'a')as f:
        f.write(log_data + '\n')

# 发送信号
login_signal.connect(login_log)
```

２．使用信号存储用户登录日志（`app.py`）

```python
from flask import Flask, request, g, render_template, redirect, url_for
from my_signal import login_signal

app = Flask(__name__)

@app.route('/')
def index():
    return 'index'

@app.route('/login', methods=['GET', 'POST'])
def login():
    error = None
    if request.method == 'POST':
        if request.form['username'] != 'admin' or \
                request.form['password'] != 'secret':
            error = 'Invalid credentials'
        else:
            g.username = request.form['username']
            login_signal.send() # 发送信号
            return redirect(url_for('index'))
    return render_template('login.html', error=error)

if __name__ == '__main__':
    app.run(debug=True)
```

> 更多 Flask 核心信号可以参考 [官方文档](http://docs.jinkan.org/docs/flask/signals.html#id6)
{.is-success}


# 4. 钩子函数

在`Flask`的一次请求生命周期的过程中，为我们提供了多个特定的装饰器装饰的函数，通过这些函数可以有效的控制我们请求的生命周期，而这些函数就叫钩子函数。

## 4.1 常用钩子函数

`before_first_request`：`Flask`项目启动后，第一次请求会执行，以后就不再执行。

`before_request`：请求已经到达了`Flask`，但是还没有进入到具体的视图函数之前调用。**如果返回为非假的值，则会直接返回响应，结束请求流程，默认可以不传，表示返回None。如果被装饰多个函数，按照函数定义顺序执行**

`after_request`：视图函数执行完成时调用，**被装饰的函数需要接受一个参数，表示`response`，该函数必须一个`response`的对象，如果修改了`response`，则直接返回修改后的结果。作用就是对视图函数处理完成的响应做进一步修改然后返回。如果被装饰多个函数，按照函数定义相反的顺序执行**

`teardown_appcontext`：在请求结束之前调用，**被装饰的函数需要接受一个参数，表示异常信息**

测试代码如下：

```python
from flask import Flask
from flask import make_response

app = Flask(__name__)

@app.route("/index/")
def index():
    return "index"

@app.before_first_request
def before_first_request():
    print("before_first_request")

@app.before_request
def before_request_1():
    print("before_request_1")
    # return "终止请求"

@app.before_request
def before_request_2():
    print("before_request_2")
    # return "终止请求"

@app.after_request
def after_request_1(response):
    print("after_request_1")
    # return make_response("aaa")
    return response

@app.after_request
def after_request_2(response):
    print("after_request_2")
    # return make_response("aaa")
    return response

@app.teardown_appcontext
def teardown(exc):
    print("teardown")

if __name__ == '__main__':
    app.run(debug=True)

"""
before_first_request
before_request_1
before_request_2
after_request_2
after_request_1
teardown
"""
```

## 4.2 其他钩子函数

- `errorhandler(code_or_exception)`：捕获的响应的异常进行处理，必须要写一个参数，来接收错误的信息，如果没有参数，就会直接报错。最后被装饰的函数需要返回相应的状态码。主要与`flask.about`配合使用，通过`about()`抛出指定异常。

```python
@app.errorhandler(404)
def page_not_found(error):  # 必须接受一个参数
    return 'This page does not exist', 404
```

- `context_processor`：使用这个钩子函数，必须返回一个字典。这个字典中的值在所有模版中都可以使用。

```python
@app.context_processor
def func2():
    return {"username": "ydongy"}
```

流程图：

![flask-hook.jpg](/assets/web框架/flask/flask-hook.jpg)

# 5. 消息闪现

Flask 提供了一个非常简单的方法来使用闪现系统向用户反馈信息。闪现系统使得在一个请求结束的时候记录一个信息，然后在且仅仅在下一个请求中访问这个数据。这通常配合一个布局模板实现。

简单来讲就是可以通过闪现消息机制把我们后台处理的成功或错误等信息提示给用户。

## 5.1 简单闪现

在`Flask`中，使用`flash message`(闪现消息)，具体使用的方法是`flash()`

```python
flash(message, category="message")

# message:消息提示
# category:消息类型，比如：message，info，error,warning。根据不同的提示可以在模板中渲染不同颜色的提示信息
```

在模板中接收`flush()`传递过来的信息使用`get_flashed_message()`

```jinja2
get_flashed_messages(with_categories, category_filter)

{# with_categories: True表示一个元组，里面是消息类型
  category_filter：将消息过滤到仅匹配提供的类别。#}
```

一个简单的案例：

```python
from flask import Flask, flash, redirect, render_template, \
    request, url_for

app = Flask(__name__)
app.secret_key = 'some_secret' # 必须配置秘钥，因为消息闪现是基于session的

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    error = None
    if request.method == 'POST':
        if request.form['username'] != 'admin' or \
                request.form['password'] != 'secret':
            error = 'Invalid credentials'
        else:
            flash('You were successfully logged in')
            return redirect(url_for('index'))
    return render_template('login.html', error=error)

if __name__ == "__main__":
    app.run(debug=True)
```

获取用户名和密码进行校验，验证成功调用`flash`方法跳转到首页，失败停留在的登录页并显示错误信息

这里是 `index.html` 模板:

```jinja2
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Index</title>
</head>
<body>
{% with messages = get_flashed_messages() %}
    {% if messages %}
        {% for message in messages %}
            <li>{{ message }}</li>
        {% endfor %}
    {% endif %}
{% endwith %}
<h3>Welcome!</h3>
<a href="{{ url_for('login') }}">login</a>
</body>
</html>
```

通过调用`get_flashed_messages`方法获取到所有的消息，然后使用`for-in`的循环显示出每一条消息。页面的底部，我们放置一个超链接，用于跳转到`login`页面

这里是`login.html`模板:

```jinja2
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
<form action="" method=post>
    <dl>
        <dt>Username:
        <dd><input type=text name=username value="{{ request.form.username }}">
        <dt>Password:
        <dd><input type=password name=password>
    </dl>
    <p><input type=submit value=Login>
</form>
{% if error %}
    <p><strong>Error</strong>: {{ error }}</p>
{% endif %}
</body>
</html>
```

启动`Flask`服务，访问 `http://127.0.0.1:5000`

![flask-闪现-1.png](/assets/web框架/flask/flask-闪现-1.png)

- 点击`Login`输入用户名和密码

![flask-闪现-2.png](/assets/web框架/flask/flask-闪现-2.png)

- 错误提交

![flask-闪现-3.png](/assets/web框架/flask/flask-闪现-3.png)

- 正确提交

![flask-闪现-4.png](/assets/web框架/flask/flask-闪现-4.png)

## 5.2 分类闪现

指定`flash('You were successfully logged in', 'info')`中的第二个参数修改闪现类型

修改模板`get_flashed_messages(with_categories=true)`中的`with_categories=True`

```jinja2
{% with messages = get_flashed_messages(with_categories=true) %}
    {% if messages %}
        <ul class=flashes>
            {% for category, message in messages %}
                <li class="{{ category }}">{{ message }}</li>
            {% endfor %}
        </ul>
    {% endif %}
{% endwith %}
```

## 5.3 过滤闪现消息

修改模板`get_flashed_messages(category_filter=["error"])`中的`category_filter`参数，以过滤函数返回的结果

```jinja2
{% with errors = get_flashed_messages(category_filter=["error"]) %}
{% if errors %}
  <ul>
    {%- for msg in errors %}
    <li>{{ msg }}</li>
    {% endfor -%}
  </ul>
{% endif %}
{% endwith %}
```

# 6. 数据流

当我们发送我们的数据远远超过内存的大小并且实时地产生这些数据时，如何才能直接把他发送给客户端，而不需要在文件系统中中转呢?

答案是生成器和 Direct Response。

## 6.1 基本使用

下面是一个简单的视图函数实时生成大量的 `CSV` 数据，这一函数使用生成器来生成数据，并且稍后激活这个生成器函数时，把返回值传递给一个 response 对象，每一个 `yield` 表达式直接被发送给浏览器。

```python
from flask import Response, Flask

app = Flask(__name__)

@app.route('/large.csv')
def generate_large_csv():
    def generate():
        for row in range(50000):
            line = []
            for col in range(500):
                line.append(str(col))
            if row % 1000 == 0:
                print('row: %d' % row)
            yield ','.join(line) + '\n'

    return Response(generate(), mimetype='text/csv')

if __name__ == '__main__':
    app.run(debug=True).
```

## 6.2 在模板中生成流

`Jinja2` 模板引擎同样支持分块逐个渲染模板：

```python
from flask import Response

def stream_template(template_name, **context):
    # 我们绕过了 Flask 的模板渲染函数，而是直接使用了模板对象，
    # 所以我们手动必须调update_template_context()函数来确保更新了模板的渲染上下文
    app.update_template_context(context)
    # 获取Jinja2的模板对象
    t = app.jinja_env.get_template(template_name)
    # 获取流式渲染模板的生成器
    rv = t.stream(context)
    # 启用缓存，这样不会每一条都发送，而是缓存满了再发送
    rv.enable_buffering(5)
    return rv

@app.route('/my-large-page.html')
def render_large_template():
    file = open("large.csv")
    return Response(stream_template('the_template.html', csv=file.readlines()))
```

# 参考

> http://www.bjhee.com/flask-ad5.html
> http://docs.jinkan.org/docs/flask/
