---
title: Flask 模板
description: 
published: true
date: 2021-03-24T14:37:40.281Z
tags: 
editor: markdown
dateCreated: 2021-03-24T14:36:48.158Z
---

# 1. 模板使用

Flask 的模板功能是基于 [Jinja2](https://jinja.palletsprojects.com/en/2.11.x/) 模板引擎实现的。接下来我们创建一个新的 Flask 运行文件，代码如下：

```python
from flask import Flask, render_template
app = Flask(__name__)
@app.route("/")
def index():
    # return render_template('index.html', username="ydy", age=18)
    context = {
        "username": "ydongy",
        "age": 23,
        "gender": "male",
        "info": {
            "phone": "17xxxxxxxx",
            "email": "example@example.com"
        },
        "nums": [1, 2, 3, 4]
    }
    return render_template('index.html', **context)
```

代码中可以发现`render_template`是主要用来渲染模板，同时可以传入多个关键字参数，也可以对一个字典进行解包的形式传递参数。

# 2. 模板变量

```jinja2
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>index</h1>
{{ username }}
{{ age }}
{{ gender }}
{{ info.phone }}
{{ info["phone"] }}
{{ info.email }}
{{ info.aaa }}
<a href="{{ url_for('login',ref="/") }}">登陆</a>
</body>
</html>
```

- `{{变量名}}`：获取模板传递过来的关键字参数，名称需要一致
- `{{变量名.键}}`：接受一个字典参数，可以通过键获取值
- `{{变量名[键]}}`：字典值的获取
- `{{url_for('视图名称',ref="当前url路径")}}`：这里的 url_for 和视图中的用法一致，用于反转获取 url 地址，ref 用于跳转之后指定是从那个页面跳转，如下的例子：

```http
http://127.0.0.1:5000/login/?ref=%2F
```

# 3. 过滤器

有时候我们想要在模版中对一些变量进行处理，那么就必须需要类似于 Python 中的函数一样，可以将这个值传到函数中，然后做一些操作。在模版中，过滤器相当于是一个函数，把当前的变量传入到过滤器中，然后过滤器根据自己的功能，再返回相应的值，之后再将结果渲染到页面中。

**基本语法**

```jinja2
{{ variable|过滤器名字 }}，使用管道符号　|　进行组合。
```

## 3.1 default 过滤器

```jinja2
1. {{ value|default('默认值') }}
2. {{ value|default('默认值',boolean=True) }}

示例：
{{ b | default('不存在',boolean=True) }}
{{ b or '不存在' }}
```

1. 表示当`value`不存在的时候，那么就会使用`default`过滤器提供的默认值
2. `bollean=True`：表示如果想要判断一个值是否为 False（例如：None、空字符串、空列表、空字典等），也可以使用`or`来替代。

## 3.2 转义过滤器

```python
@app.route("/filter/")
def my_filter():
    context = {
        "signature": '<script>alert("hello")</script>',
        "create_time": datetime.now()
    }
    return render_template('posts/filter.html', **context)
```

上面代码可以发现，传递的变量含有标准的 html 标签，如果想要获取这个字符串渲染成 html 标签的话就需要关闭自动转义，如以下的模板语法。
1. `safe`过滤器：可以关闭一个字符串的自动转义。
2. `escape`过滤器：对某一个字符串进行转义。
3. `autoescape`标签，可以对他里面的代码块关闭或开启自动转义。

```jinja2
示例：
{#关闭自动转义渲染成html标签#}
<p>个性签名:{{ signature | safe }}</p>

{#开启转义阻止渲染成html标签#}
<p>个性签名:{{ signature | escape  }}</p>

{#关闭自动转义#}
{% autoescape off %}
　　......代码块
{% endautoescape %}
```

## 3.3 常用过滤器


| 过滤器                                       | 说明                                                                                                    |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `first(value)`                               | 返回一个序列的第一个元素。`names|first`                                                                 |
| `format(value,*arags,**kwargs)`              | 格式化字符串<br>`{{ "%s" - "%s"|format('Hello?',"Foo!")}}`<br>输出：Helloo? - Foo! |
| `last(value)`                                | 返回一个序列的最后一个元素。示例：`names|last`                                                          |
| `length(value)`                              | 返回一个序列或者字典的长度。示例：`names|length`                                                        |
| `join(value,d=u'')`                          | 将一个序列用d这个参数的值拼接成字符串。                                                                 |
| `safe(value)`                                | 如果开启了全局转义，那么safe过滤器会将变量关掉转义。示例：`content_html|safe`。                         |
| `int(value)`                                 | 将值转换为 int 类型。                                                                                   |
| `float(value)`                               | 将值转换为 float 类型。                                                                                 |
| `lower(value)`                               | 将字符串转换为小写。                                                                                    |
| `upper(value)`                               | 将字符串转换为小写。                                                                                    |
| `replace(value,old,new)`                     | 替换将 old 替换为 new 的字符串。                                                                        |
| `truncate(value,length=255,killwords=False)` | 截取 length 长度的字符串。                                                                              |
| `striptags(value)`                           | 删除字符串中所有的 HTML 标签，如果出现多个空格，将替换成一个空格                                        |
| `trim`                                       | 截取字符串前面和后面的空白字符。                                                                        |
| `string(value)`                              | 将变量转换成字符串。                                                                                    |
| `wordcount(s)`                               | 计算一个长字符串中单词的个数。                                                                          |

## 3.4 自定义模板过滤器

过滤器本质上就是一个函数。如果在模版中调用这个过滤器，那么就会将这个变量的值作为第一个参数传给过滤器这个函数，然后函数的返回值会作为这个过滤器的返回值。需要使用到一个装饰器：`@app.template_filter('cut')`

```python
# 自定义过滤器，把hello替换为空字符串
@app.template_filter('cut')
def cut(value):
    value = value.replace("hello",'')
    return value
```

```jinja2
<p>{{ article|cut }}</p>
```

# 4. 判断语句

```jinja2
{% if statement %}
     ......代码块
{% endif %}
```

判断语句和python中的判断语句一致，也可以使用`>，<，<=，>=，==，!=`来进行判断，也可以通过`and，or，not，()`来进行逻辑合并操作。

# 5. for 循环

```jinja2
{% for x in range(1,10) %}
     ......代码块
{% endfor %}
```

循环语句和python中同样类似，采用`in`来遍历所有的序列以及迭代器，但是注意`jinja2`中的循环没有`break`和`continue`。如果你启用了`jinja2.ext.loopcontrols`扩展的话，你还可以在循环中使用`{% break %}`和`{% continue %}`来控制循环执行。

Jinja2 的循环内置变量主要有以下几个：

|       变量       |                         内容                          |
| :--------------: | :---------------------------------------------------: |
|   `loop.index`   |                循环迭代计数（从1开始）                |
|  `loop.index0`   |                循环迭代计数（从0开始）                |
| `loop.revindex`  |        循环迭代倒序计数（从len开始，到1结束）         |
| `loop.revindex0` |       循环迭代倒序计数（从len－1开始，到0结束）       |
|   `loop.first`   |                是否为循环的第一个元素                 |
|   `loop.last`    |               是否为循环的最后一个元素                |
|  `loop.length`   |                 循环序列中元素的个数                  |
|   `loop.cycle`   | 在给定的序列中轮循，如上例在”odd”和”even”两个值间轮循 |
|   `loop.depth`   |           当前循环在递归中的层级（从1开始）           |
|  `loop.depth0`   |           当前循环在递归中的层级（从0开始）           |

循环中使用if

```jinja2
{% for y in range(1,10) if y <= 5 %}
       ......代码块
{% endfor %}
```

# 6. 宏

模板中的宏跟python中的函数类似，可以传递参数，但是不能有返回值，可以将一些经常用到的代码片段放到宏中，然后把一些不固定的值抽取出来当成一个变量。

## 6.1 定义宏

```jinja2
{% macro input(name="",value="",type="text") %}
    <input type="{{ type }}" name="{{ name }}" value="{{ value }}">
{% endmacro %}
```

## 6.2 使用宏

- 直接导入宏内定义的函数

```jinja2
{% from "posts/macros.html" import input %}

{{ input(value="提交",type="submit") }}
```

上面代码定义宏就相当于创建一个`input`函数，参数包括:`name,value,type`，使用宏就相当于调用函数

- 导入宏，通过宏调用

```jinja2
{{ macros.input(value="提交",type="submit") }}
```

## 6.3 导入宏

```jinja2
{#引入宏#}
{% from "posts/macros.html" import input %}
{% import"posts/macros.html" as macros %}

{#把当前变量传入到宏#}
{% import"posts/macros.html" as macros with context %}
```

如果想要在导入宏的时候，就把当前模版的一些参数传给宏所在的模版，那么就应该在导入的时候使用`with context`。

## 6.4 include 标签

这个标签相当于是直接将指定的模版中的代码复制粘贴到当前位置。

例如：下面这一部分代码是公共页脚代码`templates/posts/footer.html`

```jinja2
<footer>
    底部
</footer>
```

从当前页面引入页脚代码

```jinja2
{#使用公共模板#}
<ul>
    <div class="content">
        中间
    </div>
    {% include "posts/footer.html" %}　{#相当于把代码复制在此处#}
</ul>
```

# 7. set 和 with 语句

在模版中，可以使用`set`语句来定义变量。示例如下：

```jinja2
{% set username='zhangsan' %}
<p>用户名：{{ username }}</p>
```

一旦定义了这个变量，那么在后面的代码中，都可以使用这个变量，就类似于Python的变量定义是一样的。

`with`语句定义的变量，只能在`with`语句块中使用，超过了这个代码块，就不能再使用了。示例代码如下：

- 方式一

```jinja2
{% with classroom = '一班' %}
<p>班级：{{ classroom }}</p>
{% endwith %}
```

- 方式二

```jinja2
{% with %}
    {% set age=18 %}
    <p>age: {{ age }}</p>
{% endwith %}
```

# 8. 模板继承

模版继承可以把一些公用的代码单独抽取出来放到一个父模板中。以后子模板直接继承就可以使用了。提高代码的复用性

使用`extends`语句，来指明继承的父模板。父模板的路径，也是相对于`templates`文件夹下的绝对路径

```jinja2
{% extends "base.html" %}
```

如下定义一个父模板`templates/posts/base.html`

```jinja
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>
        {% block title %}

        {% endblock %}
    </title>
</head>
<body>
我是父模板里面的代码
{% block body_block %}
    我是父模板block中的代码
{% endblock %}
</body>
</html>
```

父模板代码中定义了两个`block`语句块，相当于预留块，只要在子模板中重写`block`语句块就可以实现不同的代码。

```jinja2
{# 导入父模板 #}
{% extends "posts/base.html" %}

{# 重写title #}
{% block title %}
    sub
{% endblock %}

{% block body_block %}
{# 调用父模板block中的代码，否则就被覆盖 #}
    {{ super() }}

{# 调用另一个block中的内容 #}
    {{ self.title() }}

    我是子模板block的代码
{% endblock %}
```

# 参考

- [http://www.bjhee.com/flask-ad3.html](http://www.bjhee.com/flask-ad3.html)
- [https://dormousehole.readthedocs.io/en/latest/quickstart.html#](https://dormousehole.readthedocs.io/en/latest/quickstart.html#)

