---
title: Flask 视图
description: 
published: true
date: 2021-03-26T15:02:16.825Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:41:34.184Z
---

# 1. 函数视图

```python
app = Flask(__name__)

def my_list():
    return "列表页"

app.add_url_rule('/list/', endpoint='list', view_func=my_list)
```

代码发现，我们没有采用传统装饰器的方式定义路由，而是调用了`add_url_rule`，其中`endpoint`就是端点，如果没有填写`endpoint`，那么默认会使用`view_func`的名字，它主要还是用做`url_for`反向获取 url 使用。

> `app.route(rule,**options)`装饰器底层，底层也是使用`add_url_rule`来实现 url 与视图函数映射的。
{.is-info}


# 2. 类视图

## 2.1 标准类视图

```python
from flask import Flask, url_for, views,

class ListView(views.View):
    def dispatch_request(self, *args, **kwargs):
        return "list view"
```

上面的代码就是一个简单的标准类视图，我们必须重写了`dispatch_request`方法，以后的请求都会执行这个方法，相当于之前的是视图函数，同时也必须返回`Reponse`或者子类的对象

为类视图添加路由：

```python
app.add_url_rule('/list/', endpoint='ListView', view_func=ListView.as_view('list'))
```

1. `ListView.as_view('list')`里面必须传个参数`name`，给 view_func 起个别名，实际上就是 `dispatch_request` 函数
2. `endpoint`可以不指定，则默认使用 `view_func` 的别名（ name 参数的值），主要用于 `url_for` 反转获取 url 使用

## 2. 2 请求类视图

```python
class LoginView(views.MethodView):
    def get(self, error=None):
        return render_template('advanced/login.html', error=error)

    def post(self):
        username = request.form.get("username")
        password = request.form.get("password")
        if username == "ydy" and password == "123":
            return "success"
        else:
            # return render_template('advanced/login.html', error="用户名或密码错误")
            return self.get(error="用户名或密码错误")

    def delete(self):
        pass

    def put(self):
        pass
```

上述代码模拟了一个登录类视图，和标准类视图相比，所继承的类不同，主要是根据请求方法的不同执行不同的函数。

# 3. 视图装饰器

接下来，我们看一个模拟登陆认证的装饰器，观察一下他在函数视图和类视图的应用。

```python
from functools import wraps

# 登陆认证装饰器
def login_required(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        username = request.args.get("username")
        if username and username == "ydy":
            return func(*args, **kwargs)
        else:
            return "请登陆"

    return wrapper

@app.route("/user-settings/")
@login_required
def user_settings():
    return "用户设置页"

class ProfileView(views.View):
    decorators = (login_required,)

    def dispatch_request(self):
        return "profile"
```

通过观察可以发现，传统的视图函数的方式是通过装饰器进行装饰，达到登陆认证的效果，而且自定义的装饰器需要放在路由装饰器的下面。

而想要在类视图中使用请求登录验证的效果，需要重新定义父类的`decorators`属性

# 4. 蓝图

当我们在进行大型项目的开发时，可能需要编写大量的接口代码，同时会定义大量的路由，会导致所有的视图集中在一个文件中，非常的不便于我们的代码的管理和后期的功能代码的添加，那我们可不可以做到类似与 Django 中 app 一样，将 Flask 项目模块化，那么我们就需要使用蓝图。

蓝图作用的就是将项目模块化，相同模块的视图函数存放于统一的蓝图，然后将蓝图注册到`app`即可。

## 4.1 蓝图使用

下面就是蓝图的基本语法：

### 导入蓝图

```python
from flask import Blueprint
user_bp = Blueprint('user', __name__,url_prefix='/user')

@user_bp.route("/user-info/")
def user_info():
    return render_template('user/user_info.html')

@user_bp.route("/user-settings/")
def user_settings():
    # 注意：在蓝图中，即使是同一个文件进行url反转也要加上蓝图文件名称
    # 不能写成url_for("user_info")
    print(url_for("user.user_info"))
    return "user_settings"
```

- `user_bp=Blueprint('user', __name__,url_prefix='/user')`：定义蓝图，第一个参数指明蓝图名称，第二个参数一般为`__name__`表示当前模块，`url_prefix` 会添加到所有与该蓝图关联的 URL 前面。
- `@user_bp.route("/user-info/")`：使用蓝图定义路由，其实就是相当于单独的`app`，所以是`@user_bp`而不是`@app`
- `url_for("user.user_info")`：通过`url_for`翻转获取`url`地址，但是在蓝图中使用，需要使用 `蓝图名.xxx`，不能单独写函数名或`endpoint`的值

蓝图`url`前缀

添加前缀就表示这个蓝图所有的视图函数拥有统一的前缀，开发中对于接口`url`的定义也是按照模块划分，类似于`Django/settings`下面定义的`urls`，给每个应用统一一个前缀，前缀定义使用`url_prefix`参数指定

```python
user_bp = Blueprint('user', __name__, url_prefix="/user")

@user_bp.route("/user-settings/")
def user_settings():
    pass
```

`url_prefix`结尾不要加`“/”`，因为在视图已经加了，防止重复(`http://xxxxx/user//user-info/`)，要不就是蓝图加，视图不加，总之就是防止重复

#### 注册蓝图

```python
# 导入蓝图
from blueprints.user import user_bp
app = Flask(__name__)
# 注册蓝图
app.register_blueprint(user_bp)
```

## 4.2 蓝图模板

```python
user_bp = Blueprint('user',__name__,url_prefix='/user',template_folder='user')
```
通过`template_folder`定义模板的路径，但是项目查找模板路径还是先从项目中的`templates`中查找，如果没有查找到再去蓝图指定目录查找

## 4.3 蓝图静态文件

```python
user_bp = Blueprint('user',__name__,url_prefix='/user',static_folder='user')
url_for('user.static')
```

通常我们使用`url_for('static')`获取静态文件路径，那么就只会在`app`指定的静态文件夹目录下查找静态文件，如果想要获取蓝图中指定的静态文件目录，需要加上蓝图的名称。



