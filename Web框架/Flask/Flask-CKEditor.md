---
title: Flask-CKEditor
description: 
published: true
date: 2021-03-24T15:07:17.398Z
tags: 
editor: markdown
dateCreated: 2021-03-24T15:07:17.398Z
---

# Flask-CKEditor

> https://flask-ckeditor.readthedocs.io/en/latest/
{.is-success}

## 安装

```shell
pip install flask-ckeditor
```

## 定义使用

```python
from flask_ckeditor import CKEditor
from form import RichTextForm

app = Flask(__name__)
app.config["SECRET_KEY"] = "afaflagj2j0hg"
ckeditor = CKEditor(app)

@app.route('/')
def hello_world():
    form = RichTextForm()
    return render_template("index.html", form=form)

if __name__ == '__main__':
    app.run(debug=True)
```

```jinja2
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>Text CKEditor</h1>
<form action="" method="post">
    {{ form.csrf_token }}
    {{ form.title() }}
    {{ form.body() }}
    {{ form.submit() }}
</form>
</body>
{{ ckeditor.load() }}
</html>
```

```python
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired, Length
from flask_ckeditor import CKEditorField


class RichTextForm(FlaskForm):
    title = StringField('Title', validators=[DataRequired(), Length(1, 50)])
    body = CKEditorField('Body', validators=[DataRequired()])
    submit = SubmitField('Publish')
```

> [配置富文本编辑器](https://flask-ckeditor.readthedocs.io/en/latest/configuration.html)
{.is-success}


| 配置键               | 默认值     | 说明                                                 |
| -------------------- | ---------- | ---------------------------------------------------- |
| CKEDITOR_SERVE_LOCAL | False      | 设为 True 会使用内 置的本地资源、                    |
| CKEDITOR_PKG_TYPE    | 'standard' | CKEditor 包类型 ，可选值为 basic 、 standard 和 full |
| CKEDITOR_LANGUAGE    | ''         | 界面语言 ，传人 I SO 639 格式的语言码                |
| CKEDITOR_HEIGHT      | ''         | 编辑器高度                                           |
| CKEDITOR_WIDTH       | ''         | 编辑器宽度                                           |
