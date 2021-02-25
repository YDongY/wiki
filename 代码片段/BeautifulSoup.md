---
title: BeautifulSoup 爬取豆瓣 Top250 
description: 
published: true
date: 2021-02-22T10:13:33.629Z
tags: 
editor: undefined
dateCreated: 2021-02-22T10:13:31.151Z
---

# BeautifulSoup 爬取豆瓣 Top250 

```python
import requests
from bs4 import BeautifulSoup as bs
import time

user_agent = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36'

header = {
    'User-Agent': user_agent
}

# url = 'https://book.douban.com/top250?start=0'

def get_url_name(url):
    response = requests.get(url, headers=header)

    # print(response.text)

    bs_info = bs(response.text,'html.parser')

    for tags in bs_info.find_all('div',attrs={'class':'pl2'}): # <div class='pl2'></div>
        for tag in tags.find_all('a',):
            print(tag.get("href")) # 连接
            print(tag.get("title")) # 书名

if __name__ == "__main__":
    urls = tuple(f'https://book.douban.com/top250?start={ page * 25}' for page in range(10))

    for url in urls:
        get_url_name(url)
        time.sleep(3)
```