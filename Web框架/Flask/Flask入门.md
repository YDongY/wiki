---
title: Flask 入门
description: 
published: true
date: 2021-03-26T14:47:43.426Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:29:17.625Z
---

# 1. 安装Flask

```shell
pip install Flask -i https://pypi.doubanio.com/simple
```

# 2. Hello Flask

接下来直接入主题，创建一个 `hello.py` 的文件，之后来一套 `hello world`，直接照搬官网代码。

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello World</h1>'

if __name__ == '__main__':
    app.run()
```

上面的代码就已经是一个简单的 web 应用服务，下面对每一部分进行说明。

1. `from flask import Flask`：首先我们导入了 Flask 类。 该类的实例将会成为我们的 WSGI 应用，把客户端所有的请求都转发给这个 app 实例。
2. `app = Flask(__name__)`：创建一个 Flask 类的实例对象赋给 app。第一个参数是应用模块或者包的名称，如果你使用一个单一模块（就像本例），那么应当使用 `name`，因为名称会根据这个模块是按应用方式使用还是作为一个模块导入而发生变化（可能是 `main` ， 也可能是实际导入的名称）。 这个参数是必需的，这样 Flask 才能知道在哪里可以找到模板和静态文件等东西。
3. `@app.route('/')`：一个装饰器，指明这个函数的路由。在函数还没有调用时，python 解释器从上往下执行的时候就已经执行`app.route('/')`建立函数路由对象关系，之后返回值和@符号装饰了函数，等待请求调用被装饰的函数。
4. `def index()`：被装饰的函数，也就是路由对应的函数
5. `app.run()`：监听用户请求，如果有用户请求到来，执行`app.__call__`，底层采用`(from werkzeug.serving import run_simple)`启动 WSGI 的服务器

**启动运行**

- `flask`命令

```shell
$ export FLASK_APP = hellp.py
$ flask run
```

- `python -m`模块启动

```shell
python -m hello
```

- 当运行成功，终端内容显示如下：

```shell
 * Serving Flask app "blueprint" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 913-956-188
```

当我们执行成功，接着在浏览器访问`127.0.0.1:5000`，就可以看到`hello world`

# 3. 相关配置

> :point_right: [官方配置管理](https://dormousehole.readthedocs.io/en/latest/config.html)
{.is-success}

应用总是需要一定的配置的。根据应用环境不同，会需要不同的配置。比如开关调试模式、设置密钥以及其他依赖于环境的东西。Flask 的设计思路是在应用开始时载入配置。

不管你使用何种方式载入配置，都可以使用 Flask 对象的 config 属性来操作配置的值。 Flask 本身就使用这个对象来保存一些配置，扩展配置也可以使用这个对象保存配置。

## 3.1 配置入门

`config` 实质上是一个字典的子类， 可以像字典一样操作:

```python
app = Flask(__name__)
app.config['DEBUG'] = True
```

一次更新多个配置值可以使用 `dict.update()` 方法:

```python
app.config.update(
    DEBUG=True,
    SECRET_KEY=b'_5#y2L"F4Q8z\n\xec]/'
)
```

- **`ENV` 和 `DEBUG` 环境变量配置**

为了可靠的设置环境和调试，Flask 使用环境变量。环境由 `FLASK_ENV` 环境变量控制， 缺省为 `production` 。

- 转换到开发环境并开启调试模式，配置`FLASK_ENV`:

```shell
$ export FLASK_ENV=development
$ flask run
```

- 在开发环境的下开启调试模式：

> **开启 DEBUG 模式之后可以通过 PIN 码在网页进行错误调试。**
{.is-info}

```shell
$ export FLASK_DEBUG = 1 # 表示debug模式启动
$ flask run
```

## 3.2 配置方法

- **参数配置(字典)**

因为`app.config`是 Flask 中的 Config 类的实例，且这个类继承自`dict`，所以可以向操作字典一样操作

```python
app.config["DEBUG"] = True

app.config.update({
    "DEBUG": True,
    "SECRET_KEY": "5f352379324c22463451387a0aec5d2f"
})
```

- **环境变量配置(from_envvar)**

从指向配置文件的环境变量中加载配置，第二个参数`silent=False`表示当文件不存在时报错

```python
import os
app.config.from_pyfile(os.environ['DEBUG'],silent = False )
```

- **类配置(from_object)**

  - 实际对象引用

  ```python
  class BaseConfig(object):
      DEBUG = True

  app.config.from_object(BaseConfig)
  ```

  - 字符串：在这种情况下，将导入具有该名称的对象

  ```python
  #===============1. config.py================
  DEBUG = True

  # app 直接使用配置文件
  import config
  app.config.from_object(config)
  app.config.from_object('config')

  #===============2. config.py================
  class Config(object):
      DEBUG = True

  # app 使用配置文件中的类
  import config
  app.config.from_object(config.Config)
  app.config.from_object('config.Config')
  ```

- **Json 文件导入(from_json)**

从 JSON 文件中加载配置，第一个参数表示文件路径，第二个参数`silent=False`表示当文件不存在时报错，默认 False

```python
#=============config.json==============
{
  "DEBUG": true
}

# app使用配置文件
app.config.from_json('config.json', silent=False)
```

- **Python 文件导入(from_pyfile)**

从 Python 文件中加载配置，第一个参数表示文件路径，第二个参数 `silent=False` 表示当文件不存在时报错，默认 False

```python
#===============config.py==================
DEBUG=True

app.config.from_file('config.py',silent=False)
```

# 4. URL 与端点

```python
@app.route("/article/<string:aid>") # 字符串，默认类型。接受不包含斜杠的文本
@app.route("/article/<float:aid>")  # 正浮点数
@app.route("/article/<path:aid>")   # 字符串，包括斜杠。可以接受:/aa/bbb/aaa/xxx
@app.route("/article/<uuid:aid>")   # uuid 类型
@app.route("/article/<any:aid>")
@app.route("/article/<int:aid>")    # 接受正整数
```

参数的类型都是见名知意，主要来讲讲 `path` 和 `any` 表示什么意思：

- `path`

如下定义的两个视图，一个指定类型为 `string`，一个为 `path` ，查看它们的区别：

```python
@app.route("/article/<string:aid>")
def detail2(aid):
    return aid

@app.route("/article/<path:aid>")
def detail2(aid):
    return aid
```

当我们的 `aid=usr/bin/` 时，此时调用就是 `path` 也就是说，path 可以接受一个类似于路径的参数，而 `string` 只能接受一连串的字符，不能用 `/` 分割的字符，由于本身 `url` 地址由 `/` 分割，所以当我们想要传入一个类似目录结构的 `url`，就可以用 `path`。

- `any`

```python
# 　相当于两个视图
# /blog/<int:id>
# /user/<int:id>
@app.route("/<any(blog,user):url_path>/<int:id>")
def detail4(url_path, id):
    if url_path == "blog":
        return f"blog {id}"
    else:
        return f"user {id}"
```

从代码可以看出，`any`接受了多个不同参数，因此可以达到一个视图可以处理两个路由

- `endpoint`和 `url` 参数

```python
# /d/?wd=xxxx
@app.route("/article/",endpoint="abc")
def detail():
    wd = request.args.get("wd")
    return f"wd:{wd}"
```

一般而言我们不重新定义 `endpoint`，它主要用于 `url_for` 通过函数名反转 `url`，例如：上面的代码通过 `url_for("abc")`，结果为 `/article/`，如果不指定，默认为视图名，也就是上面的 `detail`，可以通过 `url_for("detail")` 反转。

# 5. 请求方法

```python
@app.route("/", methods=['GET', 'POST','DELETE','PUT','PATCH'])
def index():
    return "hello index"
```

通过 `methods` 参数表示当前视图可以接受那种类型的请求

# 6. 静态文件与模板路径配置

```python
app = Flask(template_folder="templates")
app = Flask(static_folder="static")
app = Flask(static_url_path="templates")
```

- `template_folder`：表示模板文件存放来指定的路径，默认当前项目目录的 templates
- `static_folder`：静态文件存放文件夹，默认 static
- `static_url_path`：主要用于改变`url`的`path`的，静态文件放在`static`下面，所以正常情况`url`是`static/filename` ，但是可以通过`static_url_path`来改变这个`url`

# 7.渲染模板

Flask 配置 Jinja2 模板引擎。使用 `render_template()` 方法可以渲染模板， 你只要提供模板名称和需要作为参数传递给模板的变量就行了。 下面是一个简单的模板渲染例子:

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

模板示例：

```jinja2
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
   <h1>Hello {{ name }}!</h1>
{% else %}
   <h1>Hello, World!</h1>
{% endif %}
```

# 8. url_for 用法

## 8.1 在视图函数中使用

`url_for`第一个参数，应该是视图函数的名字的字符串。后面的参数就是传递给`url`。如果传递的参数之前在`url`中已经定义了，那么这个参数就会被当成`path`的形式给`url`。如果这个参数之前没有在`url`中定义，那么将变成查询字符串的形式放到`url`中。

```python
@app.route('/post/list/<page>/')
def my_list(page):
    return 'my list'

print(url_for('my_list',page=1,count=2))
```

由于 page 在 my_list 视图路由定义过了，所以可以被解析成路径，而 count 没有定义，所以被解析成查询字符串。构建出来的`url：/my_list/1/?count=2`

## 8.2 在模板加载静态文件中使用

加载静态文件使用的是`url_for`函数。然后第一个参数需要为`static`，第二个参数需要为一个关键字参数`filename='路径'`。

```jinja2
{#引入静态文件#}
<link rel="stylesheet" href="{{ url_for('static',filename="css/index.css") }}">
<script type="text/javascript" src="{{ url_for('static',filename="js/index.js") }}"></script>
```

# 9. 自定义 URL 转换器

假设：一个 `url` 中，含有手机号，必须限定这个变量的字符串格式满足手机号码格式，传统的格式无法满足需求，此时就需要自定义

```python
from werkzeug.routing import BaseConverter

class ReConverter(BaseConverter):
    def __init__(self, url_map, regex):
        # 调用父类初始化方法
        super(ReConverter, self).__init__(url_map)
        # 将正则表达式保存在对象的属性中，flask 会去使用这个属性来进行路由的正则匹配
        self.regex = regex

    def to_python(self, value):
        """
        路由匹配时，匹配成功后传递给视图函数中参数的值
        :var
        """
        return value

    def to_url(self, value):
        """
        使用 url_for 反向生成 url 时，传递的参数经过该方法处理，返回的值用于生成url中的参数
        :var
        """
        val = super().to_url(value)
        return val

# 为 flask 添加自定义转换器 re
app.url_map.converters["re"] = ReConverter

# 视图使用
@app.route("/mobile/<re(r'1[34578]\d{9}'):mobile>")
def mobile():
    pass
```

我们自定义的 URL 转换器之后，并添加到`url_map`，之后在视图路由使用的时候局可以自定义正则表达式匹配任意格式

# 10. 重定向

重定向分为**永久性重定向**和**暂时性重定向**。在页面上体现的操作就是浏览器会从一个页面自动跳转到另外一个页面。

- 永久性重定向：`http`的状态码是`301`，多用于旧网址被废弃了要转到一个新的网址确保用户的访问，最经典的就是京东网站，你输入`www.jingdong.com`的时候，会被重定向到`www.jd.com`，因为`jingdong.com`这个网址已经被废弃了，被改成`jd.com`，所以这种情况下应该用永久重定向。

- 暂时性重定向：`http`的状态码是`302`，表示页面的暂时性跳转。比如访问一个需要权限的网址，如果当前用户没有登录，应该重定向到登录页面，这种情况下，应该用暂时性重定向。

**flask 使用重定向**

```python
from flask import Flask, request, url_for, redirect

@app.route("/login/")
def login():
    return "login"


@app.route("/profile/")
def profile():
    if request.args.get("name"):
        return "profile"
    else:
        return redirect(url_for('login'), code=302)
```

上面定义了两个视图，一个是登录页面，另一个是模拟认证页面，也就是当我们直接访问`/profile/`，由于视图判断没有携带 name 查询字符串，所以运行了`redirect(url_for('login'), code=302)`，先通过`url_for`解析出 login 视图的路由，然后跳转的 login 页面，而且状态码设置为 302。实际场景最多的就是购物车、个人中心访问，都会自动跳转的等于页面。

# 11. 响应

- 可以返回字符串：返回的字符串其实底层将这个字符串包装成了一个`Response`对象。
- 可以返回元组：元组的形式是(响应体,状态码,头部信息)，也不一定三个都要写，写两个也是可以的。返回的元组，其实在底层也是包装成了一个`Response`对象。
- 可以返回`Response`及其子类。

```python
from werkzeug.wrappers import Response

@app.route("/")
def test():
    # 下面三种返回的方式都是 Response() 对象
    # return "hello world"
    # return Response(response="hello world", status=200,mimetype="text/html")
    return "hello world", 200 # 数据、状态码
```

**实现一个自定义的`Response`对象：**

1. 继承自`Response`类。
2. 实现方法`force_type(cls,rv,environ=None)`。
3. 指定`app.response_class`为你自定义的`Response`对象。
4. 如果视图函数返回的数据，不是字符串，也不是元组，也不是 Response 对象，那么就会将返回值传给`force_type`，然后再将`force_type`的返回值返回给前端。

```python
from werkzeug.wrappers import Response

class JSONResponse(Response):
    @classmethod
    def force_type(cls, response, environ=None):
        # 该方法只有视图函数返回非字符，非元组，非response对象才会调用
        # 处理自己的逻辑
        return super().force_type(response,environ=environ)

app.response_class = JSONResponse

@app.route("/")
def test2():
    return [1,2,3]
```

test2 视图由于返回的结果不符合传统要求，此时就会调用我们自定义的`Response`对象，把返回值传给`force_type`方法的 response ，处理之后在调用父类方法返回


