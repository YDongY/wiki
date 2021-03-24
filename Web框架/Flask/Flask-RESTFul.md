---
title: Flask-RESTFul
description: 
published: true
date: 2021-03-24T15:02:31.626Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:01:44.035Z
---

# Flask-RESTful

> http://www.pythondoc.com/Flask-RESTful/index.html
{.is-success}

Flask-RESTful 适用于前后端分离项目的接口设计，设计符合 RESTful 风格的代码，使用简单方便。Flask-RESTful 要求 Python 版本为 2.6, 2.7, 或者 3.3及以上。

## 安装

```python
pip install flask-restful -i https://pypi.doubanio.com/simple
```

## 定义使用

下面结合 Flask-RESTful 设计一个接口，新建一个`manage.py`的文件：

```python
from flask import Flask
from flask_restful import Api, Resource

app = Flask(__name__)

api = Api(app=app)

class Index(Resource):
    def get(self):
        return {'username': "ydongy"}

api.add_resource(Index, "/", endpoint="index")
# api.add_resource(Index, "/","/index", endpoint="index") 传递多个URL

if __name__ == '__main__':
    app.run(debug=True)
```

**分析：**

1. 导入模块`from flask_restful import Api, Resource`，`Api`接受一个 app 生成一个对象，相当于把 app 包装。`Resource`是需要继承的类，替换以前的`views.MethodView`
2. `api = Api(app=app)`，把 app 包装
3. `class Index(Resource):`：定义的类视图继承`Resource`，此时返回的响应内容就是 Json 格式
4. `api.add_resource(Index, "/", endpoint="index")`：添加路由和端点，endpoint 用于 url_for 反转 url 使用，如果不写 endpoint，那么将会使用视图的名字的小写来作为 endpoint。

**构造函数参数传递**

`Api.add_resource()` 方法有两个关键字参数： `resource_class_args` 和 `resource_class_kwargs`。它们的值将被转发并传递到 Resource 实现的构造函数中

```python
from flask_restful import Resource

class TodoNext(Resource):
    def __init__(self, **kwargs):
        # smart_engine is a black box dependency
        self.smart_engine = kwargs['smart_engine']

    def get(self):
        return self.smart_engine.next_todo()

smart_engine = SmartEngine()

api.add_resource(TodoNext, '/next',
    resource_class_kwargs={ 'smart_engine': smart_engine })
```

**响应返回值**

```python
class Todo1(Resource):
    def get(self):
        # Default to 200 OK
        return {'task': 'Hello world'}

class Todo2(Resource):
    def get(self):
        # Set the response code to 201
        return {'task': 'Hello world'}, 201

class Todo3(Resource):
    def get(self):
        # Set the response code to 201 and return custom headers
        return {'task': 'Hello world'}, 201, {'Etag': 'some-opaque-string'}
```

**响应格式**

```python
@api.representation('text/csv')
def output_csv(data, code, headers=None):
    pass

@api.representation('application/json')
def output_json(data, code, headers=None):
    resp = make_response(json.dumps(data), code)
    resp.headers.extend(headers or {})
    return resp
```

这些函数必须返回 Flask Response 对象。或者在 Api 类实例化时，指定：

```python
class Api(restful.Api):
    def __init__(self, *args, **kwargs):
        super(Api, self).__init__(*args, **kwargs)
        self.representations = {
            'application/xml': output_xml,
            'text/html': output_html,
            'text/csv': output_csv,
            'application/json': output_json,
        }
```

## 参数解析

Flask-RESTful 插件提供了类似 WTForms 来验证提交的数据是否合法的包，叫做 reqparse。以下是基本用法：

```python
from flask_restful import reqparse

class Login(Resource):
    def post(self):
        parser = reqparse.RequestParser()
        parser.add_argument('username', type=str, help="用户名", required=True)
        parser.add_argument('password', type=str, help="密码", required=True)
        # eg:args['email']    # ['abc@gmail.com', '123@gmail.com']
        parser.add_argument('email', action='append')　
        parser.add_argument('phone', dest="mobile") # args['mobile']

        parser.add_argument('name', type=int, location='form')
        parser.add_argument('PageSize', type=int, location='args')
        parser.add_argument('User-Agent', location='headers')
        parser.add_argument('session_id', location='cookies')
        parser.add_argument('picture', type=werkzeug.datastructures.FileStorage, location='files')
        args = parser.parse_args()
        # args = parser.parse_args(strict=True) # 如果请求包含解析器未定义的参数，可以引发错误。

        ......
```

`add_argument`可以指定这个字段的名字，这个字段的数据类型等,也就是接受前台传递过来的请求体数据。以下将对这个方法的一些参数做详细讲解：

**常用参数：**

- `default`：默认值，如果这个参数没有值，那么将使用这个参数指定的值。
- `required`：是否必须。默认为 False，如果设置为 True，那么这个参数就必须提交上来。
- `type`：这个参数的数据类型，如果指定，那么将使用指定的数据类型来强制转换提交上来的值。
- `choices`：选项。提交上来的值只有满足这个选项中的值才符合验证通过，否则验证不通过。
- `help`：错误信息。如果验证失败后，将会使用这个参数指定的值作为错误信息。
- `trim`：是否要去掉前后的空格。
- `action='append'`：一个键接收多个值组成的列表
- `dest`：参数在解析后以其他名称存储，则可以使用 dest 关键字参数
- `location`：指定从中提取值的替代位置，**仅在 type=list 时使用 location='json' **
  - `parser.add_argument('text', location=['headers', 'values'])`指定了多个位置

**扩展 type**

其中的 type，可以使用 python 自带的一些数据类型，也可以使用`flask_restful.inputs`下的一些特定的数据类型来强制转换。比如一些常用的：

- `url`：会判断这个参数的值是否是一个url，如果不是，那么就会抛出异常。

```python
from flask_restful import inputs

parser.add_argument('urlpath', type=inputs.url, help="url地址", required=True)
```

- `regex`：正则表达式。

```python
from flask_restful import inputs

parser.add_argument('phone', type=inputs.regex(r'1[3-9]\d{9}'), help="手机号")
```

- `date`：将这个字符串转换为 `datetime.date` 数据类型。如果转换不成功，则会抛出一个异常。

```python
from flask_restful import inputs

parser.add_argument('birthday', type=inputs.date, help="生日", required=True)
```

**自定义验证**

```python
def odd_number(value):
    if value % 2 == 0:
        raise ValueError("Value is not odd")

    return value

parser.add_argument('OddNumber', type=odd_number)
```

**解析器继承**

```python
from flask_restful import reqparse

parser = reqparse.RequestParser()
parser.add_argument('foo', type=int)

parser_copy = parser.copy() # 共享解析器参数
parser_copy.add_argument('bar', type=int)

# parser_copy 既可以解析 foo ，又可以解析 bar

parser_copy.replace_argument('foo', required=True, location='json')
# 重写参数 foo

parser_copy.remove_argument('foo')
# 移除参数
```

**解析错误处理**

```python
from flask import Flask

app = Flask(__name__)

# BUNDLE_ERRORS是一个全局设置，bundle_errors 在单个RequestParser实例中会覆盖该选项。
app.config['BUNDLE_ERRORS'] = True

parser = reqparse.RequestParser(bundle_errors=True) # 覆盖全局 BUNDLE_ERRORS
parser.add_argument('foo', type=int, required=True)
parser.add_argument('bar', type=int, required=True)
```

如果没有传递 foo 和 bar，会提示类似以下错误信息：

```python
{
    "message":  {
        "foo": "foo error message",
        "bar": "bar error message"
    }
}
```

**解析错误消息**

```python
parser.add_argument('foo',choices=('one', 'two'),help='Bad choice: {error_msg}')

{
    "message":  {
        "foo": "Bad choice: three is not a valid choice",
    }
}
```

## 数据格式化

对于一个视图函数，你可以指定好一些字段用于返回。以后可以使用 ORM 模型或者自定义的模型的时候，他会自动的获取模型中的相应的字段，生成 json 数据，然后再返回给客户端。这其中需要导入`flask_restful.marshal_with`装饰器。并且需要写一个字典，来指示需要返回的字段，以及该字段的数据类型。示例代码如下：

```python
from flask_restful import marshal_with,fields

class ProfileView(Resource):
    resource_fields = {
        'username': fields.String,
        'age': fields.Integer,
        'gender': fields.Integer
    }

    @marshal_with(resource_fields)
    def get(self,user_id):
        user = User.query.get(user_id)
        return user

    # 等同于上面装饰器写法
    # def get(self,user_id):
    #     return marshal(user, resource_fields), 200
```

**分析：**

1. `resource_fields`：定义一个需要返回的 json 格式的数据变量，名称随便取
2. `@marshal_with(resource_fields)`：给方法增加一个装饰器，并把定义的变量传入

在 get 方法中，返回 user 的时候，flask_restful 会自动的读取 user 模型上的 username 以及 age 还有 gender 属性。组装成一个 json 格式的字符串返回给客户端，因此不需要我们手动调用各个字段，组织成 json 数据返回。**但是注意定义的字典中的键需要和模型字段名一致**，如果想要不一样可以增加一个参数，如下重命名属性。

**自定义字段**

可以继承 `fields.Raw` 类并实现该类的 format 功能

```python
class AllCapsString(fields.Raw):
    def format(self, value):
        return value.upper()
```

**重命名属性**

有时候返回给前台的的字段名称的不同于模型的属性名。此时可以使用`attribute`配置这种映射。比如现在想要返回 `user.username` 中的值，但是在返回给前台的时候，想要以 name 返回回去，那么可以这样写：

```python
resource_fields = {
    'name': fields.String(attribute='username')
}
```

attribute：对应模型字段属性名称，name 是返回给前端的字段名称。

还可以定义一个`default`默认值，当字段没有值的时候可以指定一个默认值。

```python
resource_fields = {
    'name': fields.String(default='Anonymous User')
}
```

**关联关系**

首先我们定义几个模型类，表示之间的关联关系：

```python
class User(db.Model):
    __tablename__ = "tb_user"
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(50))
    email = db.Column(db.String(200))


article_tag_table = db.Table(
    'tb_tag_article',
    db.Column('article_id', db.Integer, db.ForeignKey("tb_article.id"), primary_key=True),
    db.Column('tag_id', db.Integer, db.ForeignKey("tb_tag.id"), primary_key=True)
)


class Article(db.Model):
    __tablename__ = "tb_article"
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(50))
    content = db.Column(db.String(200), nullable=True)
    author_id = db.Column(db.Integer, db.ForeignKey('tb_user.id'))  # 表名.id

    author = db.relationship("User", backref="articles")

    tags = db.relationship("Tag", backref="articles", secondary=article_tag_table)


class Tag(db.Model):
    __tablename__ = "tb_tag"
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50))
```

- `user-article`：一对多的关系
- `article-tag`：多对多的关系

此时我们想要查询一个文章，但是查询结果中还要包含该文章的作者、所属标签的信息，而不是单单的外键id。此时需要如下定义，一对多的关系可以使用`fields.Nested`,多对多的关系可以使用`fields.List`。

```python
class ArticleView(Resource):
    resource_fields = {
        'title': fields.String,
        'content': fields.String,
        'author': fields.Nested({
            "id": fields.Integer,
            "username": fields.String,
            "email": fields.String
        }),
        'tags': fields.List(fields.Nested({
            "id": fields.Integer,
            "name": fields.String
        }))
    }

    @marshal_with(resource_fields)
    def get(self, article_id):
        article = Article.query.get(article_id)
        return article
```

- `author`：就是 Article 关联字段的属性，表示单个，所以使用 `fields.Nested`，参数是想要获取的关联对象的属性
- `tags`：也是 Article 关联字段的属性，表示多个，所以使用 `fields.List`，而每一个`tag`又是一个关联对象，所以嵌套一层`fields.Nested`，参数是想要获取的关联对象的属性

**返回结果如下：**

```json
{
    "title": "mysql",
    "content": "入门到删库",
    "author": {
        "id": 1,
        "username": "jack",
        "email": "123@163.com"
    },
    "tags": [
        {
            "id": 1,
            "name": "database"
        },
        {
            "id": 2,
            "name": "language"
        }
    ]
}
```

## 装饰器

Resource 该类上有一个名为 `method_decorators` 的属性 。可以对 Resource 添加自己的装饰器

```python
def authenticate(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        if not getattr(func, 'authenticated', True):
            return func(*args, **kwargs)

        acct = basic_authentication()  # custom account lookup function

        if acct:
            return func(*args, **kwargs)

        flask_restful.abort(401)
    return wrapper


class Resource(flask_restful.Resource):
    method_decorators = [authenticate]   # applies to all inherited resources
```

或者，您可以指定一个可迭代的字典，该字典映射到 HTTP 方法，并且装饰器将仅应用于匹配的请求。

```python
def cache(f):
    @wraps(f)
    def cacher(*args, **kwargs):
        # caching stuff
    return cacher

class MyResource(restful.Resource):
    method_decorators = {'get': [cache]}

     def get(self, *args, **kwargs):
        return something_interesting(*args, **kwargs)

     def post(self, *args, **kwargs):
        return create_something(*args, **kwargs)
```

在这种情况下，缓存装饰器将仅应用于 GET 请求，而不应用于 POST 请求。

## 自定义错误处理程序

Flask-RESTful 将 `handle_error()` 在Flask-RESTful 路由上发生的任何 400 或 500 错误上调用该函数，而其他路由则保持不变，如果希望捕获 404 错误，可以再 Api 构造函数添加　`catch_all_404s`

```python
app = Flask(__name__)
api = flask_restful.Api(app, catch_all_404s=True)
```

如果当发生错误时，想做一些特别的事情比如，发送电子邮件等。可以使用 `got_request_exception()` 方法将自定义错误处理程序附加到异常。

```python
def log_exception(sender, exception, **extra):
    """ Log an exception to our logging framework """
    sender.logger.debug('Got exception during processing: %s', exception)

from flask import got_request_exception
got_request_exception.connect(log_exception, app)
```

**自定义错误消息**

当请求期间遇到某些错误时，希望返回特定的消息和状态。可以告诉 Flask-RESTful 如何处理每个错误/异常，这样就不必用 try/except API 代码了

```python
errors = {
    'UserAlreadyExistsError': {
        'message': "A user with that username already exists.",
        'status': 409,
    },
    'ResourceDoesNotExist': {
        'message': "A resource with that ID no longer exists.",
        'status': 410,
        'extra': "Any extra information you want.",
    },
}

app = Flask(__name__)
api = flask_restful.Api(app, errors=errors)
```

## 蓝图及模板使用

- 在蓝图中，如果使用`flask-restful`，那么在创建`Api`对象的时候，就不要再使用`app`了，而是使用蓝图app。
- 如果在`flask-restful`的视图中想要返回`html`代码，或者是模版，那么就应该使用`api.representation`这个装饰器来定义一个函数，在这个函数中，应该对`html`代码进行一个封装，再返回。

```python
from flask import render_template, make_response
from flask import Blueprint
from flask_restful import Api

api_bp = Blueprint(name="v1", import_name=__name__, url_prefix="/v1")
api = Api(app=api_bp)

@api.representation('text/html')
def html(data, code, headers):
    resp = make_response(data)
    return resp


class ListView(Resource):
    def get(self):
        return render_template("index.html")

api.add_resource(ListView, '/article/', endpoint="list_view")
```

**分析：**

1. `api_bp = Blueprint(name="v1", import_name=__name__, url_prefix="/v1")`：定义蓝图
2. `api = Api(app=api_bp)`：传入蓝图 app 创建 Api 对象
3. `@api.representation('text/html')`：装饰一个函数，指明返回的 `content-type` 类型，该方法自动执行，所以需要返回一个 response 对象
4. `return render_template("index.html")`：restful 类视图返回模板，会自动执行上面被装饰器定义过的函数
