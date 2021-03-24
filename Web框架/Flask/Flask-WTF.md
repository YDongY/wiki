---
title: Flask-WTF
description: 
published: true
date: 2021-03-24T15:05:38.195Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:05:02.599Z
---

# Flask-WTF

> http://docs.jinkan.org/docs/flask/patterns/wtforms.html
{.is-success}


## 安装

```shell
pip install flask-wtf
```

Flask-WTF 默认为每个表单启用 CSRF 保护，它会为我们自动生成和验证 CSRF 令牌 ，在实例化表羊类时添加一个包含 CSRF 令牌值的隐藏字段，字段名为 **csrf token** 。 默认情况下， Flask-WTF 使用程序密钥来对 CSRF 令牌进行签名，所以我们需要为程序设置密钥：

```python
app.secret_key = 'secret string'
```

## 定义表单

```python
from wtforms import Form, BooleanField, TextField, PasswordField, validators
from wtforms.validators import DataRequired,Length

class LoginForm(Form):
    username = StringField('Username',validators=[DataRequired()])
    password = PasswordField('Password',validators=[DataRequired(),Length(6,32)])
    remember = BooleanField("Remember me")
    submit = SubmitField("Log in")
```

字段属性的名称将作为对应 HTML <input＞元素的 name 属性及 id 属性值 

- **常用的 WTForm字段**

| 字段类        | 说明                                       | 对应 HTML 表示                      |
| ------------- | ------------------------------------------ | ----------------------------------- |
| BooleanField  | 复选框                                     | `<input type="checkbox">`           |
| DateField     | 文本字段，值被处理为 datetiem.datetime对象 | `<input type="text">`               |
| DateTimeField | 文本字段，值被处理为 datetiem.date对象     | `<input type="text">`               |
| FileField     | 文件上传                                   | `<input type="file">`               |
| FloatField    | 浮点数                                     | `<input type="text">`               |
| IntegerField  | 整数                                       | `<input type="text">`               |
| RadioField    | 一组单选按钮                               | `<input type="radio">`              |
| SelectField   | 下拉列表                                   | `<select<option></option></select>` |
| SubmitField   | 提交按钮                                   | `<input type="submit">`             |
| StringField   | 文本字段                                   | `<input type="text">`               |
| PasswordField | 密码文本                                   | `<input type="password">`           |
| TextAreaField | 多行文本                                   | `<textarea></textarea>`             |

- **常用属性**

`render_kw`：渲染 HTML 的 placeholder 字段值

```python
username = StringngField('Username',render_kw={'placeholder':"Your Username"})
```

输出的 HTML 代码

```html
<input type="text" id="username" name="username" placeholder="Your Username">
```

`validators`：包含一系列验证器 ，会在表单提交后被逐一调用验证表单数据

```python
password = PasswordField('Password',validators=[DataRequired(),Length(6,32)])
```

`label`：字段标签`<label>`的值，也就是渲染后显示在输入字段前的文字

- **常用验证器**

| 验证器                                  | 说明                             |
| --------------------------------------- | -------------------------------- |
| DataRequired(message=None)              | 验证数据是否有效                 |
| Email(message=None)                     | 验证邮箱合法性                   |
| EqualTo(fieldname,message=None)         | 验证两个字段值是否相同           |
| InputRequired(message=None)             | 验证是否有数据                   |
| Length(min=-1 , max=-l , message=None)  | 验证输入值长度是否在给定范 围 内 |
| Optional(strip whitespace=True)         | 允许输入值为空 ， 并跳过其他验证 |
| Regexp(regex, fl ags=O, message=Non巳） | 使用正则表达式验证输入值         |

message 参数用来传入自定义错误消息，如过没有设置直则使用内置的英文错误消息 

- **自定义验证器**

```python
captcha = StringField(validators=[Length(4,4)])

def validate_captcha(self,field):
    if field.data.isdigit(): # 验证码是否为存数字
        raise ValidationError('验证码错误！')
```

- **文件上传验证器**

```python
from flask_wtf.file import FileField,FileRequired,FileAllowed
from flask_wtf import FlaskForm

class UploadForm(FlaskForm):
    photo = FileField('Upload',validators=FileRequired(),FileAllowed(['jpg','jpeg','png','gif']))
    submit = SubmitField()
```

```jinja2
<form method="post" enctype="multipart/form-data">
    {{ form.csrf_token }}
    {{ form_field(form.photo) }}
    {{ form.submit }}
</form>
```

## 输出 HTML 代码

实例属性转换成字符串或直接调用就可以获取表单字段对应的 HTML 代码：

```shell
>>> form = LoginForm ()
>>> form.username()
u'<input id="username" name="username" type="text" value="">'
>>> form.submit ()
u'<input id="username" name="submit" type="submit" value="submit">'
```

在调用字段属性时，通过添加括号使用关键字参数的形式也可以传入字段额外的 HTML 属性 ：

```shell
>>> form.password(class="form-control form-control-lg is-invalid")
```

## 在模板中渲染表单

首先在视图函数里实例化表单类 LoginForm ，然后在 `render_template()` 函数中使用关键字参数 form 将表单实例传入模板

```python
from forms import LoginForm

@app.route('/loing')
def login():
    form = LoginForm()
    return render_template("login.html", form=form)
```

在模板中，只需要调用表单类的属性即可获取字段对应的 HTML 代码，如果需要传入参数，也可以添加括号

```jinja2
<form method="post">
    {{ form.csrf_token }}
    {{ form.username.label }}{{ form.username }}<br>
    {{ form.password.label }}{{ form.password }}<br>
    {{ form.remember }}{{ form.remember.label }}<br>
    {{ form.submit }}<br>
</form>
```

Flask-WTF 为表单类实例提供 了一个 `form.hidden_tag()` 方法，这个方法会依次渲染表单中所有的隐藏字段。 因为 csrf_token 字段也是隐藏字段，所以当这个方法被调用时也会渲染 csrf_token 字段。

## 处理表单提交数据

```python
@app.route("/login", methods=["GET", "POST"])
def login():
    # ......
    form = LoginForm()
    """
    if request.method=="POST" and form.validate:
    	# ....
    """
    # 或者
    if form.validate_on_submit():
        # 提交并验证，验证通过返回 True，获取表单提交的数据
        # username = form.username.date
        flash(f'Welcome home, {form.username.data}')
        return redirect(url_for('index'))
    return render_template("login.html", form=form)
```
