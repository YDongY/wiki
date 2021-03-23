---
title: Django 视图
description: 
published: true
date: 2021-03-23T13:03:24.633Z
tags: 
editor: markdown
dateCreated: 2021-03-23T13:01:52.430Z
---

# 请求限制 Method

## 限制请求装饰器

1. `django.http.decorators.http.require_http_methods`

这个装饰器需要传递一个允许访问的方法的列表。比如只能通过 GET 的方式访问。那么示例代码如下：

```python
from django.views.decorators.http import require_http_methods


@require_http_methods(["GET"])
def my_view(request):
    pass
```

2. `django.views.decorators.http.require_GET`

这个装饰器相当于是 `require_http_methods(['GET'])` 的简写形式，只允许使用 GET 的 method 来访问视图

```python
from django.views.decorators.http import require_GET


@require_GET
def my_view(request):
    pass
```

3. `django.views.decorators.http.require_POST`

这个装饰器相当于是 `require_http_methods(['POST'])` 的简写形式，只允许使用 POST 的 method 来访问视图

```python
from django.views.decorators.http import require_POST


@require_POST
def my_view(request):
    pass
```

4. `django.views.decorators.http.require_safe`

这个装饰器相当于是 `require_http_methods(['GET','HEAD'])` 的简写形式，只允许使用相对安全的方式来访问视图。因为 GET 和 HEAD 不会对服务器产生增删改的行为。因此是一种相对安全的请求方式

```python
from django.views.decorators.http import require_safe


@require_safe
def my_view(request):
    pass
```

# 重定向

重定向分为永久性重定向和暂时性重定向：

- 永久性重定向：http 的状态码是 301，多用于旧网址被废弃了要转到一个新的网址确保用户的访问，最经典的就是京东网站，你输入 `www.jingdong.com`
  的时候，会被重定向到 `www.jd.com`，因为 `jingdong.com` 这个网址已经被废弃了，被改成 `jd.com`，所以这种情况下应该用永久重定向。
- 暂时性重定向：http 的状态码是 302，表示页面的暂时性跳转。比如访问一个需要权限的网址，如果当前用户没有登录，应该重定向到登录页面，这种情况下，应该用暂时性重定向。

在 Django 中，重定向是使用 `redirect(to, *args, permanent=False, **kwargs)`来实现的

- to：是一个 url
- permanent：代表的是这个重定向是否是一个永久的重定向，默认是 False

```python
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import redirect, reverse


def profile(request):
    username = request.GET.get('username')
    if username:
        return HttpResponse('个人中心')
    else:
        # return redirect(reverse('signup'))
        return HttpResponseRedirect(reverse('signup'))
```

# WSGIRequest 对象

Django 在接收到 http 请求之后，会根据 http 请求携带的参数以及报文信息创建一个 WSGIRequest 对象，并且作为视图函数第一个参数传给视图函数。也就是我们经常看到的 request 参数。

在这个对象上我们可以找到客户端上传上来的所有信息。这个对象的完整路径是 `django.core.handlers.wsgi.WSGIRequest`。

## WSGIRequest 对象常用属性

- `path`：请求服务器的完整“路径”，但不包含域名和参数。
- `method`：代表当前请求的http方法。比如是 `GET` 还是 `POST`。
- `GET`：一个 `django.http.request.QueryDict` 对象。操作起来类似于字典。这个属性中包含了所有以`?xxx=xxx`的方式上传上来的参数。
- `POST`：也是一个 `django.http.request.QueryDict` 对象。这个属性中包含了所有以 `POST` 方式上传上来的参数。
- `FILES`：也是一个 `django.http.request.QueryDict` 对象。这个属性中包含了所有上传的文件。
- `COOKIES`：一个标准的 Python 字典，包含所有的 cookie，键值对都是字符串类型。
- `session`：一个类似于字典的对象。用来操作服务器的 session。
- `META`：存储的客户端发送上来的所有 header 信息。
- `CONTENT_LENGTH`：请求的正文的长度（是一个字符串）。
- `CONTENT_TYPE`：请求的正文的`MIME`类型。
- `HTTP_ACCEPT`：响应可接收的 `Content-Type`。
- `HTTP_ACCEPT_ENCODING`：响应可接收的编码。
- `HTTP_ACCEPT_LANGUAGE`： 响应可接收的语言。
- `HTTP_HOST`：客户端发送的 HOST 值。
- `HTTP_REFERER`：在访问这个页面上一个页面的 url。
- `QUERY_STRING`：单个字符串形式的查询字符串（未解析过的形式）。
- `REMOTE_ADDR`：客户端的 IP 地址。如果服务器使用了 nginx 做反向代理或者负载均衡，那么这个值返回的是 `127.0.0.1`
  ，这时候可以使用 `HTTP_X_FORWARDED_FOR` 来获取，所以获取 ip 地址的代码片段如下：

```python
def index(request):
    if request.META.has_key('HTTP_X_FORWARDED_FOR'):
        ip = request.META['HTTP_X_FORWARDED_FOR']
    else:
        ip = request.META['REMOTE_ADDR']
```

- `REMOTE_HOST`：客户端的主机名。
- `REQUEST_METHOD`：请求方法。一个字符串类似于GET或者POST。
- `SERVER_NAME`：服务器域名。
- `SERVER_PORT`：服务器端口号，是一个字符串类型。

## WSGIRequest 对象常用方法

- `is_secure()`：是否是采用 `https` 协议。
- `is_ajax()`：是否采用 `ajax` 发送的请求。原理就是判断请求头中是否存在 `X-Requested-With:XMLHttpRequest`。
- `get_host()`：服务器的域名。如果在访问的时候还有端口号，那么会加上端口号。比如 `www.baidu.com:9000`。
- `get_full_path()`：返回完整的 `path`。如果有查询字符串，还会加上查询字符串。比如 `/music/bands/?print=True`。
- `get_raw_uri()`：获取请求的完整 `url`。

## QueryDict 对象

我们平时用的 `request.GET` 和 `request.POST` 都是 `QueryDict` 对象，这个对象继承自 `dict`，因此用法跟 `dict` 相差无几。其中用得比较多的是 get 方法和 getlist 方法。

- `get` 方法：用来获取指定 key 的值，如果没有这个 key，那么会返回 None。

```python
get('键', 默认值)
```  

- `getlist` 方法：如果浏览器上传上来的 key 对应的值有多个，根据键获取值，值以列表返回，如果键不存在则返回空列表 `[]`

```python
getlist('键', 默认值)
```

# HttpResponse 对象

Django 服务器接收到客户端发送过来的请求后，会将提交上来的这些数据封装成一个 HttpRequest 对象传给视图函数。那么视图函数在处理完相关的逻辑后，也需要返回一个响应给浏览器。

而这个响应，我们必须返回 HttpResponseBase 或者他的子类的对象。而 HttpResponse 则是 HttpResponseBase 用得最多的子类。那么接下来就来介绍一下 HttpResponse 及其子类。

## 常用属性：

- `content`：返回的内容。
- `status_code`：返回的 HTTP 响应状态码。
- `content_type`：返回的数据的 MIME 类型，默认为 `text/html`。浏览器会根据这个属性，来显示数据。如果是 `text/html`
  ，那么就会解析这个字符串，如果 `text/plain`，那么就会显示一个纯文本。 常用的 `Content-Type` 如下：
    - `text/html`（默认的，html 文件）
    - `text/plain`（纯文本）
    - `text/css`（css 文件）
    - `text/javascript`（js 文件）
    - `multipart/form-data`（文件提交）
    - `application/json`（json 传输）
    - `application/xml`（xml 文件）
- 设置请求头：`response['X-Access-Token'] = 'xxxx'`。

## 常用方法

- `set_cookie`：用来设置 cookie 信息。后面讲到授权的时候会着重讲到。
- `delete_cookie`：用来删除 cookie 信息。
- `write`：HttpResponse 是一个类似于文件的对象，可以用来写入数据到数据体（content）中。

## HttpResponse 子类

Django 提供了一系列 HttpResponse 的子类，可以快速设置状态码

- HttpResponseRedirect 301
- HttpResponsePermanentRedirect 302
- HttpResponseNotModified 304
- HttpResponseBadRequest 400
- HttpResponseNotFound 404
- HttpResponseForbidden 403
- HttpResponseNotAllowed 405
- HttpResponseGone 410
- HttpResponseServerError 500

# JsonResponse 类

用来对象 dump 成 json 字符串，然后返回将 json 字符串封装成 Response 对象返回给浏览器。并且他的 `Content-Type` 是`application/json`。示例代码如下：

```python
from django.http import JsonResponse


def index(request):
    return JsonResponse({"username": "张三", "age": 18})
```

默认情况下 JsonResponse 只能对字典进行 dump，如果想要对非字典的数据进行 dump，那么需要给 JsonResponse 传递一个`safe=False` 参数。示例代码如下：

```python
from django.http import JsonResponse


def index(request):
    persons = ['张三', '李四', '王五']
    return JsonResponse(persons, safe=False)
```

# 生成 CSV

## 生成小的 CSV 文件

这里将用一个生成小的 CSV 文件为例，来把生成 CSV 文件的技术要点讲到位。我们用 Python 内置的 csv 模块来处理 csv 文件，并且使用 HttpResponse 将 csv 文件返回回去。示例代码如下：

```python
import csv
from django.http import HttpResponse


def csv_view(request):
    # 初始化 HttpResponse 的时候，指定了 Content-Type 为 text/csv
    # 这将告诉浏览器是一个 csv 格式的文件而不是一个 HTML 格式的文件，默认值就是 html
    response = HttpResponse(content_type='text/csv')

    # 在 response 中添加一个 Content-Disposition 头
    # attachment; 浏览器将不会对这个文件进行显示，而是作为附件的形式下载
    # filename="somefilename.csv" 是用来指定这个 csv 文件的名字
    response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'

    # csv 模块的 writer 方法，将相应的数据写入到 response
    writer = csv.writer(response)
    writer.writerow(['username', 'age', 'height', 'weight'])
    writer.writerow(['张三', '18', '180', '110'])

    return response
```

## CSV 模板

将 csv 格式的文件定义成模板，然后使用 Django 内置的模板系统，并给这个模板传入一个 Context 对象，这样模板系统就会根据传入的 Context 对象，生成具体的 csv 文件。示例代码如下：

```django
{% for row in data %}"{{ row.0|addslashes }}", "{{ row.1|addslashes }}", "{{ row.2|addslashes }}", "{{ row.3|addslashes }}", "{{ row.4|addslashes }}"
{% endfor %}
```

这个简短的模板遍历所给的数据，并为每行生成一个 CSV 行。它利用 addslashes 模板过滤器确保引号不会引发任何问题

视图函数：

```python
from django.http import HttpResponse
from django.template import loader, Context


def some_view(request):
    response = HttpResponse(content_type='text/csv')
    response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'

    csv_data = (
        ('First row', 'Foo', 'Bar', 'Baz'),
        ('Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"),
    )

    t = loader.get_template('my_template_name.txt')
    response.write(t.render({"data": csv_data}))
    return response
```

## 生成大的 CSV 文件

服务器要生成一个大型 csv 文件，需要的时间可能会超过浏览器默认的超时时间）。这时候我们可以借助另外一个类，叫做 StreamingHttpResponse 对象，这个对象是将响应的数据作为一个流返回给客户端，而不是作为一个整体返回。示例代码如下 ：

```python
import csv
from django.http import StreamingHttpResponse


class Echo:
    """
    定义一个可以执行写操作的类，以后调用 csv.writer 的时候，就会执行这个方法
    """

    def write(self, value):
        return value


def large_csv(request):
    # 生成器
    rows = ([f"Row {idx},{idx}"] for idx in range(655360))
    pseudo_buffer = Echo()
    # 执行 csv.writer(pseudo_buffer) 的时候，就会调用 Echo.writer 方法
    writer = csv.writer(pseudo_buffer)
    response = StreamingHttpResponse((writer.writerow(row) for row in rows),
                                     content_type="text/csv")
    response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'
    return response
```

- StreamingHttpResponse

这个类是专门用来处理流数据的。使得在处理一些大型文件的时候，不会因为服务器处理时间过长而到时连接超时。这个类不是继承自 `HttpResponse`，并且跟 `HttpResponse` 对比有以下几点区别：

1. 这个类没有属性 content，相反是 `streaming_content`。
2. 这个类的 `streaming_content` 必须是一个可以迭代的对象。
3. 这个类没有 write 方法，如果给这个类的对象写入数据将会报错。

> 注意：StreamingHttpResponse 会启动一个进程来和客户端保持长连接，所以会很消耗资源。所以如果不是特殊要求，尽量少用这种方法。

# 类视图

## View

`django.views.generic.base.View `是主要的类视图，所有的类视图都是继承自它。如果我们写自己的类视图，也可以继承自他。然后再根据当前请求的 method，来实现不同的方法。

比如这个视图只能使用 get 的方式来请求，那么就可以在这个类中定义 `get(self,request,*args,**kwargs)` 方法。以此类推，如果只需要实现 post 方法，那么就只需要在类中实现 `post(self,request,*args,**kwargs)`。示例代码如下：

```python
from django.views import View
from django.shortcuts import render


class BookDetailView(View):
    def get(self, request, *args, **kwargs):
        return render(request, 'detail.html')
```

类视图写完后，还应该在 `urls.py` 中进行映射，映射的时候就需要调用 View 的类方法 `as_view()` 来进行转换。示例代码如下：

```python
urlpatterns = [
    path("detail/<book_id>/", views.BookDetailView.as_view(), name='detail')
]
```

> View 支持以下方法 `['get','post','put','patch','delete','head','options','trace']`。

如果用户访问了 View 中没有定义的方法。比如你的类视图只支持 get 方法，而出现了 post 方法，那么就会把这个请求转发给`http_method_not_allowed(request,*args,**kwargs)`。示例代码如下：

```python
class AddBookView(View):
    def post(self, request, *args, **kwargs):
        return HttpResponse("书籍添加成功！")

    def http_method_not_allowed(self, request, *args, **kwargs):
        return HttpResponse("您当前采用的method是：%s，本视图只支持使用post请求！" % request.method)
```

> 不管是 get 请求还是 post 请求，都会走 `dispatch(request,*args,**kwargs)` 方法，所以如果实现这个方法，将能够对所有请求都处理到。

## TemplateView

`django.views.generic.base.TemplateView`，这个类视图是专门用来返回模版的。在这个类中，有两个属性是经常需要用到的

1. `template_name`，这个属性是用来存储模版的路径，TemplateView 会自动的渲染这个变量指向的模版。
2. `get_context_data`，这个方法是用来返回上下文数据的，也就是在给模版传的参数的。

示例代码如下：

```python
from django.views.generic import TemplateView


class HomePageView(TemplateView):
    template_name = "home.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['username'] = "Jack"
        return context
```

在 urls.py 中的映射代码如下：

```python
from django.urls import path

urlpatterns = [
    path('', HomePageView.as_view(), name='home'),
]
```

如果在模版中不需要传递任何参数，那么可以直接只在 urls.py 中使用 TemplateView 来渲染模版。示例代码如下：

```python
from django.urls import path
from django.views.generic import TemplateView

urlpatterns = [
    path('about/', TemplateView.as_view(template_name="about.html")),
]
```

## ListView

```python
from django.views.generic import ListView


class ArticleListView(ListView):
    model = Article  # 重写 model 类属性，指定这个列表是给哪个模型的
    template_name = 'article_list.html'  # 指定这个列表的模板
    paginate_by = 10  # 指定这个列表一页中展示多少条数据
    context_object_name = 'articles'  # 指定这个列表模型在模板中的参数名称
    ordering = 'create_time'  # 指定这个列表的排序方式
    page_kwarg = 'page'  # 获取第几页的数据的参数名称。默认是 page

    def get_context_data(self, **kwargs):
        # 获取上下文的数据
        context = super(ArticleListView, self).get_context_data(**kwargs)
        print(context)
        return context

    def get_queryset(self):
        """ 自定义查询集 """
        # 如果你提取数据的时候，并不是要把所有数据都返回，那么你可以重写这个方法。将一些不需要展示的数据给过滤掉
        return Article.objects.filter(id__lte=89)
```

# Paginator 和 Page 类

Paginator 和 Page 类都是用来做分页的。分别定义在 `django.core.paginator.Paginator` 和 `django.core.paginator.Page` 以下对这两个类的常用属性和方法做解释：

## Paginator 常用属性和方法

- `count`：总共有多少条数据。
- `num_pages`：总共有多少页。
- `page_range`：页面的区间。比如有三页，那么就 `range(1,4)`。

## Page 常用属性和方法

- `has_next`：是否还有下一页。
- `has_previous`：是否还有上一页。
- `next_page_number`：下一页的页码。
- `previous_page_number`：上一页的页码。
- `number`：当前页。
- `start_index`：当前这一页的第一条数据的索引值。
- `end_index`：当前这一页的最后一条数据的索引值。

# 给类视图添加装饰器

在开发中，有时候需要给一些视图添加装饰器。如果用函数视图那么非常简单，只要在函数的上面写上装饰器就可以了。但是如果想要给类添加装饰器，那么可以通过以下两种方式来实现：

- 装饰 dispatch 方法

```python
from django.utils.decorators import method_decorator


def login_required(func):
    def wrapper(request, *args, **kwargs):
        if request.GET.get("username"):
            return func(request, *args, **kwargs)
        else:
            return redirect(reverse('index'))

    return wrapper


class IndexView(View):
    def get(self, request, *args, **kwargs):
        return HttpResponse("index")

    @method_decorator(login_required)
    def dispatch(self, request, *args, **kwargs):
        super(IndexView, self).dispatch(request, *args, **kwargs)
```

- 直接装饰在整个类上

```python
from django.utils.decorators import method_decorator


def login_required(func):
    def wrapper(request, *args, **kwargs):
        if request.GET.get("username"):
            return func(request, *args, **kwargs)
        else:
            return redirect(reverse('login'))

    return wrapper


@method_decorator(login_required, name='dispatch')
class IndexView(View):
    def get(self, request, *args, **kwargs):
        return HttpResponse("index")

    def dispatch(self, request, *args, **kwargs):
        super(IndexView, self).dispatch(request, *args, **kwargs)
```

# 错误处理

常用的错误码：

- 404：服务器没有指定的 url。
- 403：没有权限访问相关的数据。
- 405：请求的 method 错误。
- 400：bad request，请求的参数错误。
- 500：服务器内部错误，一般是代码出 bug 了。
- 502：一般部署的时候见得比较多，一般是 nginx 启动了，然后 uwsgi 有问题。

自定义错误模板：

在碰到比如 404，500 错误的时候，想要返回自己定义的模板。那么可以直接在 `templates` 文件夹下创建相应错误代码的html模板文件。那么以后在发生相应错误后，会将指定的模板返回回去。

```
$ tree
.
├── 404.html
└── 400.html
```

错误处理解决方案：

对于 404和 500 这种自动抛出的错误。我们可以直接在 templates 文件夹下新建相应错误代码的模板文件。而对于其他的错误，我们可以专门定义一个 app，用来处理这些错误。

```python
from django.shortcuts import render


def view_403(request):
    return render(request, 'errors/403.html', status=403)


def view_400(request):
    return render(request, 'errors/400.html', status=400)
```