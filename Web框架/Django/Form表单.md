---
title: Django 表单
description: 
published: true
date: 2021-03-23T14:48:51.561Z
tags: 
editor: markdown
dateCreated: 2021-03-23T14:48:23.510Z
---

## Django 中的表单

Django 中的表单丰富了传统的 HTML 语言中的表单。在 Django 中的表单，主要做以下两件事：

1. 渲染表单模板。
2. 表单验证数据是否合法

这里以一个做一个留言板为例。首先我们在后台服务器定义一个表单类，继承自 `django.forms.Form`。示例代码如下：

```python
# forms.py
from django import forms


class MessageBoardForm(forms.Form):
    title = forms.CharField(max_length=3, label='标题', min_length=2,
                            error_messages={"min_length": '标题字符段不符合要求！'})
    content = forms.CharField(widget=forms.Textarea, label='内容')
    email = forms.EmailField(label='邮箱')
    reply = forms.BooleanField(required=False, label='回复')
```

然后在视图中，根据是 GET 还是 POST 请求来做相应的操作。如果是 GET 请求，那么返回一个空的表单，如果是 POST 请求，那么将提交上来的数据进行校验。示例代码如下：

```python
# views.py
from django.views import View
from django.shortcuts import render
from django.http import HttpResponse


class IndexView(View):
    def get(self, request):
        form = MessageBoardForm()
        return render(request, 'index.html', {'form': form})

    def post(self, request):
        form = MessageBoardForm(request.POST)
        if form.is_valid():
            title = form.cleaned_data.get('title')
            content = form.cleaned_data.get('content')
            email = form.cleaned_data.get('email')
            reply = form.cleaned_data.get('reply')
            return HttpResponse('success')
        else:
            print(form.errors)
            return HttpResponse('fail')
```

1. GET 请求的时候，传了一个 form 给模板生成一个表单的 html 代码。
2. POST 请求的时候，根据前端上传上来的数据，构建一个新的表单，这个表单是用来验证数据是否合法的，如果数据都验证通过了，那么我们可以通过 `cleaned_data`
   来获取相应的数据。在模板中渲染表单的 HTML 代码如下：

```html

<form action="" method="post">
    <table>
        {{ form.as_table }}
        <tr>
            <td></td>
            <td><input type="submit" value="提交"></td>
        </tr>
    </table>
</form>
```

# 表单验证 Field

使用 Field 可以是对数据验证的第一步。你期望这个提交上来的数据是什么类型，那么就使用什么类型的Field。

## CharField

用来接收文本。

参数：

- `max_length`：这个字段值的最大长度。
- `min_length`：这个字段值的最小长度。
- `required`：这个字段是否是必须的。默认是必须的。
- `error_messages`：在某个条件验证失败的时候，给出错误信息。

## EmailField

用来接收邮件，会自动验证邮件是否合法。

错误信息的 key：`required、invalid`

## FloatField

用来接收浮点类型，并且如果验证通过后，会将这个字段的值转换为浮点类型。

参数：

- `max_value`：最大的值。
- `min_value`：最小的值。

错误信息的 key：`required、invalid、max_value、min_value`。

## IntegerField

用来接收整形，并且验证通过后，会将这个字段的值转换为整形。

参数：

- `max_value`：最大的值。
- `min_value`：最小的值。

错误信息的 key：`required、invalid、max_value、min_value`。

## URLField

用来接收 url 格式的字符串。

错误信息的 key：`required、invalid`。

# 常用验证器

在验证某个字段的时候，可以传递一个 `validators` 参数用来指定验证器，进一步对数据进行过滤

- `MaxValueValidator`：验证最大值。
- `MinValueValidator`：验证最小值。
- `MinLengthValidator`：验证最小长度。
- `MaxLengthValidator`：验证最大长度。
- `EmailValidator`：验证是否是邮箱格式。
- `URLValidator`：验证是否是 URL 格式。
- `RegexValidator`：如果还需要更加复杂的验证，那么我们可以通过正则表达式的验证器。比如现在要验证手机号码是否合格，那么我们可以通过以下代码实现：

```python
from django import forms
from django.core import validators


class MyForm(forms.Form):
    telephone = forms.CharField(
        validators=[validators.RegexValidator("1[345678]\d{9}", message='请输入正确格式的手机号码！')])
```

## 自定义验证

对某个字段进行自定义的验证方式是，定义一个方法，这个方法的名字定义规则是：`clean_fieldname`。

如果验证失败，那么就抛出一个验证错误。比如要验证用户表中手机号码之前是否在数据库中存在，那么可以通过以下代码实现：

```python
from django import forms
from django.core import validators


class MyForm(forms.Form):
    telephone = forms.CharField(
        validators=[validators.RegexValidator("1[345678]\d{9}", message='请输入正确格式的手机号码！')])

    def clean_telephone(self):
        telephone = self.cleaned_data.get('telephone')
        exists = User.objects.filter(telephone=telephone).exists()
        if exists:
            raise forms.ValidationError("手机号码已经存在！")
        return telephone
```

以上是对某个字段进行验证，如果验证数据的时候，需要针对多个字段进行验证，那么可以重写 `clean` 方法。比如要在注册的时候，要判断提交的两个密码是否相等。那么可以使用以下代码来完成：

```python
from django import forms
from django.core import validators


class MyForm(forms.Form):
    telephone = forms.CharField(
        validators=[validators.RegexValidator("1[345678]\d{9}", message='请输入正确格式的手机号码！')])
    pwd1 = forms.CharField(max_length=12)
    pwd2 = forms.CharField(max_length=12)

    def clean(self):
        cleaned_data = super().clean()
        pwd1 = cleaned_data.get('pwd1')
        pwd2 = cleaned_data.get('pwd2')
        if pwd1 != pwd2:
            raise forms.ValidationError('两个密码不一致！')
```

## 提取错误信息

如果验证失败了，那么有一些错误信息是我们需要传给前端的。这时候我们可以通过以下属性来获取：

- `form.errors`：这个属性获取的错误信息是一个包含了 html 标签的错误信息
- `form.errors.get_json_data()`：这个方法获取到的是一个字典类型的错误信息。将某个字段的名字作为 key，错误信息作为值的一个字典
- `form.as_json()`：这个方法是将 `form.get_json_data()` 返回的字典 dump 成 json 格式的字符串，方便进行传输

```python
{
    'username': [
        {
            'message': 'Enter a valid URL.',
            'code': 'invalid'
        },
        {
            'message': 'Ensure this value has at most 4 characters (it has 22).',
            'code': 'max_length'
        }
    ]
}
```

如果我只想把错误信息放在一个列表中，而不要再放在一个字典中。这时候我们可以定义一个方法，把这个数据重新整理一份。实例代码如下：

```python
from django import forms


class MyForm(forms.Form):
    username = forms.URLField(max_length=4)

    def get_errors(self):
        errors = self.errors.get_json_data()
        new_errors = {}
        for key, message_dicts in errors.items():
            messages = []
            for message in message_dicts:
                messages.append(message['message'])
            new_errors[key] = messages
        return new_errors
```

# ModelForm

表单中的 Field 和模型中的 Field 基本上是一模一样的，而且表单中需要验证的数据，也就是我们模型中需要保存的。那么这时候我们就可以将模型中的字段和表单中的字段进行绑定。

比如现在有个 Article 的模型。示例代码如下：

```python
from django.db import models
from django.core import validators


class Article(models.Model):
    title = models.CharField(max_length=10,
                             validators=[validators.MinLengthValidator(limit_value=3)])
    content = models.TextField()
    author = models.CharField(max_length=100)
    category = models.CharField(max_length=100)
    create_time = models.DateTimeField(auto_now_add=True)
```

那么在写表单的时候，就不需要把 Article 模型中所有的字段都一个个重复写一遍了。示例代码如下：

```python
from django import forms


class MyForm(forms.ModelForm):  # 继承自forms.ModelForm
    class Meta:
        model = Article  # 对应的模型
        fields = "__all__"  # 表单验证所有字段
```

如果只想针对其中几个字段进行验证，那么可以给 fields 指定一个列表，将需要的字段写进去。比如只想验证 title 和 content， 那么可以使用以下代码实现：

```python
from django import forms


class MyForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'content']
```

如果要验证的字段比较多，只是除了少数几个字段不需要验证，那么可以使用 exclude 来代替 fields。比如我不想验证 category，那么示例代码如下：

```python
class MyForm(forms.ModelForm):
    class Meta:
        model = Article
        exclude = ['category']
```

使用 ModelForm，因为字段都不是在表单中定义的，而是在模型中定义的，因此一些错误消息无法在字段中定义。那么这时候可以在Meta 类中，定义 `error_messages`
，然后把相应的错误消息写到里面去。示例代码如下：

```python
from django import forms


class MyForm(forms.ModelForm):
    class Meta:
        model = Article
        exclude = ['category']
        error_messages = {
            'title': {
                'max_length': '最多不能超过10个字符！',
                'min_length': '最少不能少于3个字符！'
            },
            'content': {
                'required': '必须输入content！',
            }
        }
```

## save 方法

ModelForm 还有 save 方法，可以在验证完成后直接调用 save 方法，就可以将这个数据保存到数据库中了。示例代码如下：

```python
from django.http import HttpResponse


def index(request):
    form = MyForm(request.POST)
    if form.is_valid():
        form.save()
        return HttpResponse('succes')
    else:
        print(form.get_errors())
        return HttpResponse('fail')
```

这个方法必须要在 clean 没有问题后才能使用，如果在 clean 之前使用，会抛出异常。

另外，我们在调用 save 方法的时候，如果传入一个 `commit=False`
，那么只会生成这个模型的对象，而不会把这个对象真正的插入到数据库中。比如表单上验证的字段没有包含模型中所有的字段，这时候就可以先创建对象，再根据填充其他字段，把所有字段的值都补充完成后，再保存到数据库中。示例代码如下：

```python
from django.http import HttpResponse


def index(request):
    form = MyForm(request.POST)
    if form.is_valid():
        article = form.save(commit=False)
        article.category = 'Python'
        article.save()
        return HttpResponse('succes')
    else:
        print(form.get_errors())
        return HttpResponse('fail')
```

# 文件上传

- 前端 HTML 代码实现

1. 在前端中，我们需要填入一个 form 标签，然后在这个 form 标签中指定 `enctype="multipart/form-data"`，不然就不能上传文件。
2. 在 form 标签中添加一个 input 标签，然后指定 input 标签的 name，以及 type="file"。

以上两步的示例代码如下：

```html

<form action="" method="post" enctype="multipart/form-data">
    <input type="file" name="myfile">
</form>
```

- 后端的代码实现

后端的主要工作是接收文件。然后存储文件。接收文件的方式跟接收 POST 的方式是一样的，只不过是通过 `request.FILES` 来实现。示例代码如下：

```python
from django.shortcuts import render
from django.http import HttpResponse


def save_file(file):
    with open('somefile.txt', 'wb') as fp:
        for chunk in file.chunks():
            fp.write(chunk)


def index(request):
    if request.method == 'GET':
        form = MyForm()
        return render(request, 'index.html', {'form': form})
    else:
        myfile = request.FILES.get('myfile')
        save_file(myfile)
        return HttpResponse('success')
```

## 使用模型来处理上传的文件

在定义模型的时候，我们可以给存储文件的字段指定为 FileField，这个 Field 可以传递一个 `upload_to` 参数，用来指定上传上来的文件保存到哪里。比如我们让他保存到项目的
files 文件夹下，那么示例代码如下：

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.db import models


# models.py
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    thumbnail = models.FileField(upload_to="files")


# views.py
def index(request):
    if request.method == 'GET':
        return render(request, 'index.html')
    else:
        title = request.POST.get('title')
        content = request.POST.get('content')
        thumbnail = request.FILES.get('thumbnail')
        article = Article(title=title, content=content, thumbnail=thumbnail)
        article.save()
        return HttpResponse('success')
```

## 指定 MEDIA_ROOT 和 MEDIA_URL

以上我们是使用了 `upload_to` 来指定上传的文件的目录。我们也可以指定 `MEDIA_ROOT`，就不需要在 `FileField`中指定`upload_to`，他会自动的将文件上传到 `MEDIA_ROOT` 的目录下。

```python
MEDIA_ROOT = os.path.join(BASE_DIR,'media')
MEDIA_URL = '/media/'
```

然后我们可以在 `urls.py` 中添加 `MEDIA_ROOT` 目录下的访问路径。示例代码如下：

```python
from django.urls import path
from front import views
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    # ...
] + static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)

```

如果我们同时指定 `MEDIA_ROOT` 和 `upload_to`，那么会将文件上传到 `MEDIA_ROOT` 下的 `upload_to` 文件夹中。示例代码如下：

```python
from django.db import models
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    thumbnail = models.FileField(upload_to="%Y/%m/%d/")
```

## 限制上传的文件拓展名

如果想要限制上传的文件的拓展名，那么我们就需要用到表单来进行限制。我们可以使用普通的 Form 表单，也可以使用 ModelForm，直接从模型中读取字段。示例代码如下：

```python
from django.db import models
from django import forms
from django.core import validators
# models.py
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    thumbnail = models.FileField(upload_to='%Y/%m/%d/',validators=[validators.FileExtensionValidator(['txt','pdf'])])

# forms.py
class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = "__all__"
```

## 上传图片

上传图片跟上传普通文件是一样的。只不过是上传图片的时候 Django 会判断上传的文件是否是图片的格式（除了判断后缀名，还会判断是否是可用的图片）。如果不是，那么就会验证失败。我们首先先来定义一个包含 `ImageField` 的模型。示例代码如下：

```python
from django.db import models
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    thumbnail = models.ImageField(upload_to="%Y/%m/%d/")
```

> 注意：使用 ImageField，必须要先安装 Pillow 库：pip install pillow
{.is-warning}


因为要验证是否是合格的图片，因此我们还需要用一个表单来进行验证。表单我们直接就使用 ModelForm 就可以了。示例代码如下：

```python
from django import forms
class MyForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = "__all__"
```

