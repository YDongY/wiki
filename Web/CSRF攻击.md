---
title: CSRF 攻击
description: 
published: true
date: 2021-03-24T06:24:42.008Z
tags: 
editor: markdown
dateCreated: 2021-03-24T06:24:19.904Z
---

# 什么是 CSRF

CSRF（Cross Site Request Forgery）中文名称：跨站请求伪造，缩写为：CSRF/XSRF。

通常可以理解为：攻击者盗用了你的身份，以你的名义发送恶意请求。

CSRF 能够做的事情包括：以你名义发送邮件，发消息，盗取你的账号，甚至于购买商品，虚拟货币转账......

造成的问题包括：个人隐私泄露以及财产安全。

# CSRF 攻击原理

![csrf.png](/assets/web/csrf.png)

从上图可以看出，要完成一次CSRF攻击，受害者必须依次完成两个步骤：

1. 登录受信任网站 A，并在本地生成 Cookie。
2. 在不登出 A 的情况下，访问危险网站 B。

简单的概述一下就是：当用户登录某个网站 A 获取到 Cookie，在 Cookie 没有失效的情况下访问了另一个危险网站 B（伪造某个请求方式）可能默认触发事件达到用户不知情的情况下携带之前的 Cookie 请求网站 A，从而迷惑服务端请求成功，造成危害。

# CSRF 防御

## 增加伪随机数

1. Cookie Hashing(所有表单都包含同一个伪随机值)
2. 在表单里增加 Hash 值，以认证这确实是用户发送的请求。
3. 表单提交后，然后在服务器端进行 Hash 值验证

例如：Django 的 CSRF 中间件，需要在表单指定 `csrf_token`

## 验证码

要求每次的用户提交都需要用户在表单中填写一个图片上的随机字符串