---
title: selenium webdriver 模拟浏览器登录豆瓣
description: 
published: true
date: 2021-02-22T12:19:18.485Z
tags: 
editor: undefined
dateCreated: 2021-02-22T12:09:33.059Z
---

# 安装

- 安装 selenium 包

```python
pip3 install -i https://pypi.doubanio.com/simple selenium
```

- 下载 [chromedriver](https://chromedriver.chromium.org/)

并把文件放在可直接执行的位置，可以通过把文件移动到 `/usr/local/bin` 目录下，也可以通过添加环境变量的的方式

> webdriver 依赖不同的浏览器，这里推荐使用 Chrome，且保持 Chrome 与 chromedriver 版本一致
{.is-warning}

# webdriver 模拟登录

```python
from selenium import webdriver
import time

try:
    browser = webdriver.Chrome()
    browser.get('https://www.douban.com')
    time.sleep(1)

    browser.switch_to_frame(browser.find_elements_by_tag_name('iframe')[0])
    btm1 = browser.find_element_by_xpath('/html/body/div[1]/div[1]/ul[1]/li[2]')
    btm1.click()

    browser.find_element_by_xpath('//*[@id="username"]').send_keys('手机号/邮箱')
    browser.find_element_by_id('password').send_keys('密码')
    time.sleep(1)
    browser.find_element_by_xpath('//a[contains(@class,"btn-account")]').click()

    cookies = browser.get_cookies() # 获取cookies
    print(cookies)
    time.sleep(3)

except Exception as e:
    print(e)
finally:    
    browser.close()
```