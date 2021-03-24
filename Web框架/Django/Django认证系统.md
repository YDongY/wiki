---
title: Django 认证系统
description: 
published: true
date: 2021-03-24T11:05:34.726Z
tags: 
editor: markdown
dateCreated: 2021-03-24T11:05:34.726Z
---

# Django 中的用户认证

Django 有一个内置的用户认证系统。用来处理用户、分组、权限以及基于 cookie 的会话系统。Django 的认证系统包括：

- 验证：检验用户是否是他们的用户
- 授权：决定已验证用户能做什么

认证系统由以下部分组成：

- 用户
- 权限：二进制标识指定用户是否可以执行特定任务
- 分组
- 可以配置的密码哈希系统
- 可插拔的后台管理系统

# 使用授权系统

默认情况下，创建完一个 django 项目后，其实就已经集成了授权系统，所有的配置在 `settings.py` 中：

- INSTALLED_APPS
    1. `django.contrib.auth`：包含了一个核心授权框架，以及大部分的模型定义
    2. `django.contrib.contenttypes`：Content Type 系统，可以用来关联模型和权限

- MIDDLEWARE
    1. `SessionMiddleware`：用来管理 session
    2. `AuthenticationMiddleware`：用来处理和当前 session 相关联的用户

# User 模型

User 模型是认证系统的核心，源代码位于：`django.contrib.auth.models.User`

内置的 User 模型拥有以下的字段：

- `username``： 用户名。150 个字符以内。可以包含数字和英文字符，以及`_`、`@`、`+`、`.`和`-`字符。不能为空，且必须唯一！
- `first_name`：30 个字符以内。可以为空
- `last_name`：150 个字符以内。可以为空
- `email`：邮箱。可以为空
- `password`：密码。经过哈希过后的密码
- `groups`：分组。一个用户可以属于多个分组，一个分组可以拥有多个用户。groups 这个字段是跟 Group 的一个多对多的关系
- `user_permissions`：权限。一个用户可以拥有多个权限，一个权限可以被多个用户所有用。和Permission属于一种多对多的关系
- `is_staff`：是否可以进入到 admin 的站点。代表是否是员工
- `is_active`：是否是可用的。对于一些想要删除账号的数据，我们设置这个值为False就可以了，而不是真正的从数据库中删除
- `is_superuser`：是否是超级管理员。如果是超级管理员，那么拥有整个网站的所有权限。
- `last_login`：上次登录的时间。
- `date_joined`：账号创建的时间。

## 创建用户

通过 `create_user` 方法可以快速的创建用户。这个方法必须要传递 `username`、`email`、`password`。示例代码如下：

```python
from django.contrib.auth.models import User

user = User.objects.create_user('Jack', 'jack@gmail.com', '111111')
# 此时 user 对象已经存储到数据库中了。当然你还可以继续使用 user 对象进行一些修改
user.last_name = 'jack-aka'
user.save()
```

## 创建超级用户

创建超级用户有两种方式。第一种是使用代码的方式。用代码创建超级用户跟创建普通用户非常的类似，只不过是使用 `create_superuser`。示例代码如下：

```python
from django.contrib.auth.models import User

User.objects.create_superuser('admin', 'admin@gmail.com', '111111')
```

也可以通过命令行的方式。命令如下：

```shell
$ python manage.py createsuperuser
Username (leave blank to use 'ydongy'): root
Email address: root@gmail.com
Password: 
Password (again): 
Superuser created successfully.
```

后面就会提示你输入用户名、邮箱以及密码。

## 修改密码

默认情况下，Django 使用带有 SHA256 哈希的 PBKDF2 算法对密码经过加密后才存储进去的。所以如果想要修改密码，不能直接修改 `password`
字段，而需要通过调用 `set_password` 来达到修改密码的目的。示例代码如下：

```python
from django.contrib.auth.models import User

user = User.objects.get(pk=1)
user.set_password('新的密码')
user.save()
```

## 登录验证

Django 的验证系统已经帮我们实现了登录验证的功能。通过 `django.contrib.auth.authenticate` 即可实现。这个方法只能通过 `username`
和 `password` 来进行验证。示例代码如下：

```python
from django.contrib.auth import authenticate

user = authenticate(username='admin', password='111111')
# 如果验证通过了，那么就会返回一个 user 对象。
if user is not None:
    # 执行验证通过后的代码
    pass
else:
    # 执行验证没有通过的代码。
    pass
```

# 扩展用户模型

## 设置 Proxy 模型

如果你对 Django 提供的字段，以及验证的方法都比较满意，没有什么需要改的。但是只是需要在他原有的基础之上增加一些操作的方法。那么建议使用这种方式。示例代码如下：

```python
from django.contrib.auth.models import User


class Person(User):
    class Meta:
        proxy = True

    @classmethod
    def get_blacklist(cls):
        """ 获取所有黑名单的人 
            >>> Person.get_blacklist()
        """
        return cls.objects.filter(is_active=False)
```

在以上，我们定义了一个 Person 类，让他继承自 User，并且在 Meta 中设置 `proxy=True`，说明这个只是 User 的一个代理模型。他并不会影响原来 User
模型在数据库中表的结构。并且 `User.objects.all()` 和 `Person.objects.all()` 其实是等价的。因为他们都是从 User 这个模型中获取所有的数据。

## 一对一外键扩展

如果你对用户验证方法 `authenticate` 没有其他要求，就是使用 `username` 和 `password`
即可完成。但是想要在原来模型的基础之上添加新的字段，那么可以使用一对一外键的方式。示例代码如下：

```python
from django.contrib.auth.models import User
from django.db import models
from django.dispatch import receiver
from django.db.models.signals import post_save


class UserExtension(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='extension')
    birthday = models.DateField(null=True, blank=True)
    school = models.CharField(max_length=100)


@receiver(post_save, sender=User)
def create_user_extension(sender, instance, created, **kwargs):
    # 信号处理
    if created:
        # 第一次创建进行绑定
        UserExtension.objects.create(user=instance)
    else:
        instance.extension.save()


# views.py
from django.http import HttpResponse


def index(request):
    user = User.objects.create_user('Jack', 'jack@gmail.com', '111111')
    user.extension.school = '家里蹲大学'
    user.save()  # 触发信号
    return HttpResponse('create one-to-one user success')
```

以上定义一个 `UserExtension` 的模型，并且和 User 模型进行一对一的绑定，以后我们新增的字段，就添加到 `UserExtension`
上。并且还写了一个接受保存模型的信号处理方法，只要是 User 调用了 save 方法，那么就会创建一个 UserExtension 和User 进行绑定。

## 继承自 AbstractUser

继承自 `django.contrib.auth.models.AbstractUser`，这个类也是 `django.contrib.auth.models.User` 的父类。比如我们想要在原来
User 模型的基础之上添加一个 `telephone` 和 `school` 字段。示例代码如下：

```python
from django.db import models
from django.contrib.auth.models import AbstractUser
from django.contrib.auth.base_user import BaseUserManager


class UserManager(BaseUserManager):
    use_in_migrations = True

    def _create_user(self, telephone, password, **extra_fields):
        if not telephone:
            raise ValueError("请填入手机号码！")
        user = self.model(telephone=telephone, *extra_fields)
        user.set_password(password)
        user.save()
        return user

    def create_user(self, telephone, password, **extra_fields):
        extra_fields.setdefault('is_superuser', False)
        return self._create_user(telephone, password)

    def create_superuser(self, telephone, password, **extra_fields):
        extra_fields['is_superuser'] = True
        return self._create_user(telephone, password)


class User(AbstractUser):
    telephone = models.CharField(max_length=11, unique=True)
    school = models.CharField(max_length=100)

    # 指定 telephone 作为 USERNAME_FIELD，以后使用 authenticate
    # 函数验证的时候，就可以根据 telephone 来验证而不是原来的 username
    USERNAME_FIELD = 'telephone'
    REQUIRED_FIELDS = []

    # 重新定义 Manager 对象，在创建 user 的时候使用 telephone 和 password，而不是使用 username 和 password
    objects = UserManager()
```

然后在 `settings.py` 中配置好 `AUTH_USER_MODEL=youapp.User`。

> 这种方式因为破坏了原来 User 模型的表结构，所以必须要在第一次 migrate 前就先定义好。

## 继承自 AbstractBaseUser 模型

1. 创建模型。示例代码如下：

```python
from django.contrib.auth.base_user import AbstractBaseUser
from django.contrib.auth.models import PermissionsMixin
from django.db import models


class User(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(unique=True)
    username = models.CharField(max_length=150)
    telephone = models.CharField(max_length=11, unique=True)
    is_active = models.BooleanField(default=True)

    USERNAME_FIELD = 'telephone'
    REQUIRED_FIELDS = []

    objects = UserManager()

    def get_full_name(self):
        return self.username

    def get_short_name(self):
        return self
```

- `USERNAME_FIELD`：用来描述 User 模型名字字段的字符串，作为唯一的标识。如果没有修改，那么会使用 USERNAME 来作为唯一字段
- `REQUIRED_FIELDS`：一个字段名列表，用于当通过 createsuperuser 管理命令创建一个用户时的提示。
- `is_active`：一个布尔值，用于标识用户当前是否可用
- `get_full_name()`：获取完整的名字
- `get_short_name()`：一个比较简短的用户名

2. 重新定义 UserManager

因为默认的 UserManager 在创建用户的时候使用的是 `username` 和 `password` ，那么我们要替换成 `telephone` 。示例代码如下：

```python
from django.contrib.auth.base_user import BaseUserManager


class UserManager(BaseUserManager):
    use_in_migrations = True

    def _create_user(self, telephone, password, **extra_fields):
        if not telephone:
            raise ValueError("请填入手机号码！")
        user = self.model(telephone=telephone, *extra_fields)
        user.set_password(password)
        user.save()
        return user

    def create_user(self, telephone, password, **extra_fields):
        extra_fields.setdefault('is_superuser', False)
        return self._create_user(telephone, password)

    def create_superuser(self, telephone, password, **extra_fields):
        extra_fields['is_superuser'] = True
        return self._create_user(telephone, password)
```

3. 在创建了新的 User 模型后，还需要在 `settings.py` 中配置好。配置 `AUTH_USER_MODEL='appname.User'`

4. 如何使用这个自定义的模型

比如以后我们有一个 Article 模型，需要通过外键引用这个 User 模型，那么可以通过以下方式引用：

```python
from django.db import models
from django.contrib.auth import get_user_model
from myauth.models import User


class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    # 或者 
    # author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    # author = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)
```

# 权限和分组

## 登录

在使用 authenticate 进行验证后，如果验证通过了。那么会返回一个 user 对象，拿到 user 对象后，可以使用 `django.contrib.auth.login `
进行登录。示例代码如下：

```python
from django.contrib.auth import login, authenticate
from django.shortcuts import render, redirect, reverse
from django.http import HttpResponse


def my_login(request):
    if request.method == 'GET':
        return render(request, 'login.html')
    form = LoginForm(request.POST)
    if form.is_valid():
        telephone = form.cleaned_data.get('telephone')
        password = form.cleaned_data.get('password')
        remember = form.cleaned_data.get('remember')
        user = authenticate(request, username=telephone, password=password)
        if user and user.is_active:
            login(request, user)  # 内置登录本质已经设置了 session
        else:
            return HttpResponse("手机号或密码错误")
        if remember:
            request.session.set_expiry(None)
        else:
            request.session.set_expiry(0)
        return HttpResponse("登录成功")
    print(form.errors)
    return redirect(reverse('login'))
```

## 注销

注销，可以通过 `django.contrib.auth.logout` 来实现。他会清理掉这个用户的 session 数据。

```python
from django.contrib.auth import logout
from django.http import HttpResponse


def my_logout(request):
    logout(request)
    return HttpResponse('退出成功')
```

## 登录限制

有时候，某个视图函数是需要经过登录后才能访问的。那么我们可以通过下面几种方式：

- 原始方式

```python
from django.conf import settings
from django.shortcuts import redirect


def my_view(request):
    if not request.user.is_authenticated:
        # 认证失败跳转到登录页面
        return redirect('%s?next=%s' % (settings.LOGIN_URL, request.path))
```

- `django.contrib.auth.decorators.login_required` 装饰器

```python
from django.contrib.auth.decorators import login_required


# 在验证失败后，会重定向到 settings.LOGIN_URL
# 并传递绝对路径到查询字符串中。例如： /accounts/login/?next=/polls/3/ 
# settings.LOGIN_URL 默认：`/accounts/login/`
@login_required(login_url='/accounts/login/')
def my_view(request):
    pass
```

默认情况下，成功验证时用户跳转的路径保存在名为 `"next"` 的查询字符串参数中。如果你希望这个参数使用不同名称，请在 `login_required()`
中传递可选参数 `redirect_field_name` ：

```python
from django.contrib.auth.decorators import login_required


# 例如： /accounts/login/?my_redirect_field=/polls/3/ 
@login_required(redirect_field_name='my_redirect_field')
def my_view(request):
    ...
```

- `django.contrib.auth.mixins import LoginRequiredMixin`基于类的视图，与 `login_required` 效果相同

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views import View


class MyView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'redirect_to'
```

## 权限

Django 中内置了权限的功能。他的权限都是针对表或者说是模型级别的。比如对某个模型上的数据是否可以进行增删改查操作。他不能针对数据级别的。

创建完一个模型后，针对这个模型默认就有四种权限，分别是 `add`、`delete`、`change`、`view`。可以在执行完 `migrate`
命令后，查看数据库中的`auth_permission` 表中的所有权限

| id | content\_type\_id | codename | name |
| :--- | :--- | :--- | :--- |
| 1 | 1 | add\_logentry | Can add log entry |
| 2 | 1 | change\_logentry | Can change log entry |
| 3 | 1 | delete\_logentry | Can delete log entry |
| 4 | 1 | view\_logentry | Can view log entry |
| 5 | 2 | add\_permission | Can add permission |
| 6 | 2 | change\_permission | Can change permission |
| 7 | 2 | delete\_permission | Can delete permission |
| 8 | 2 | view\_permission | Can view permission |
| 9 | 3 | add\_group | Can add group |
| 10 | 3 | change\_group | Can change group |
| 11 | 3 | delete\_group | Can delete group |
| 12 | 3 | view\_group | Can view group |
| 13 | 4 | add\_user | Can add user |
| 14 | 4 | change\_user | Can change user |
| 15 | 4 | delete\_user | Can delete user |
| 16 | 4 | view\_user | Can view user |
| 17 | 5 | add\_contenttype | Can add content type |
| 18 | 5 | change\_contenttype | Can change content type |
| 19 | 5 | delete\_contenttype | Can delete content type |
| 20 | 5 | view\_contenttype | Can view content type |
| 21 | 6 | add\_session | Can add session |
| 22 | 6 | change\_session | Can change session |
| 23 | 6 | delete\_session | Can delete session |
| 24 | 6 | view\_session | Can view session |
| 25 | 7 | add\_userextension | Can add user extension |
| 26 | 7 | change\_userextension | Can change user extension |
| 27 | 7 | delete\_userextension | Can delete user extension |
| 28 | 7 | view\_userextension | Can view user extension |

- 通过定义模型添加权限

```python
from django.db import models
from django.contrib.auth import get_user_model


class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey(get_user_model(), on_delete=models.CASCADE)

    class Meta:
        permissions = (
            ('view_article', 'can view article'),
        )
```

- 通过代码添加权限

权限都是 `django.contrib.auth.Permission` 的实例。这个模型包含三个字段，`name`、`codename` 以及 `content_type`
，其中的 `content_type` 表示这个 `permission` 是属于哪个 app 下的哪个 models。用 `Permission` 模型创建权限的代码如下：

```python
from django.contrib.auth.models import Permission, ContentType
from .models import Article

content_type = ContentType.objects.get_for_model(Article)
permission = Permission.objects.create(name='可以编辑的权限', codename='edit_article',
                                       content_type=content_type)
```

## 用户与权限管理

权限本身只是一个数据，必须和用户进行绑定，才能起到作用。User 模型和权限之间的管理，可以通过以下几种方式来管理：

1. `user.user_permissions.set(permission_list)`：直接给定一个权限的列表。
2. `user.user_permissions.add(permission,permission,...)`：一个个添加权限。
3. `user.user_permissions.remove(permission,permission,...)`：一个个删除权限。
4. `user.user_permissions.clear()`：清除权限。
5. `user.has_perm('<app_name>.<codename>')`：判断是否拥有某个权限。权限参数是一个字符串，格式是 `app_name.codename`。
6. `user.get_all_permissons()`：获取所有的权限。

```python
from django.contrib.auth.models import User, ContentType, Permission
from django.http import HttpResponse


def operate_permissions(request):
    user = User.object.first()
    content_type = ContentType.objects.get_for_modle(Article)
    permissions = Permission.objects.filter(content_type=content_type)
    for permission in permissions:
        print(permission)
    user.user_permissions.set(permissions)
    return HttpResponse('success')
```

## 权限限定装饰器

使用 `django.contrib.auth.decorators.permission_required`
可以非常方便的检查用户是否拥有这个权限，如果拥有，那么就可以进入到指定的视图函数中，如果不拥有，那么就会报一个 403 错误。示例代码如下：

```python
from django.contrib.auth.decorators import permission_required


@permission_required('appname.view_article', login_url='/accounts/login/', raise_exception=True)
# @permission_required(['appname.view_article','appname.add_article'], login_url='/accounts/login/', raise_exception=True)
def my_view(request):
    ...
```

# 分组

通过把多个权限分为一组，然后把需要赋予这些权限的用户添加到这个分组中，从而有效的管理每个用户的权限。即一个分组中的所有用户具有相同的权限。

分组我们使用的是 `django.contrib.auth.models.Group` 模型， 每个用户组拥有 `id` 和 `name` 两个字段，该模型在数据库被映射为 `auth_group`
数据表

1. `Group.object.create(group_name)`：创建分组。
2. `group.permissions`：某个分组上的权限。多对多的关系。
    - `group.permissions.add`：添加权限。
    - `group.permissions.remove`：移除权限。
    - `group.permissions.clear`：清除所有权限。
    - `user.get_group_permissions()`：获取用户所属组的权限。
3. `user.groups`：某个用户上的所有分组。多对多的关系。

# 在模板中使用权限

在 `settings.TEMPLATES.OPTIONS.context_processors` 下，因为添加了 `django.contrib.auth.context_processors.auth` 上下文处理器，因此在模板中可以直接通过 `perms` 来获取用户的所有权限。示例代码如下：

```django
{% if perms.appname.add_article%}
  ......
{% endif %}
```

