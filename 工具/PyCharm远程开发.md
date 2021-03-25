---
title: PyCharm 远程开发配置
description: 
published: true
date: 2021-03-25T08:37:10.776Z
tags: 
editor: markdown
dateCreated: 2021-03-25T08:36:04.171Z
---

在本地创建好工程项目（或从git上clone下代码）后，用 Pycharm 打开：

# 1. 打开「Tools」设置 「Deployment」

![pycharm远程配置1.png](/assets/工具/pycharm远程配置/pycharm远程配置1.png)

# 2. 选择 SFTP

![pycharm远程配置2.png](/assets/工具/pycharm远程配置/pycharm远程配置2.png)

# 3. 设置服务器名字

![pycharm远程配置3.png](/assets/工具/pycharm远程配置/pycharm远程配置3.png)

# 4. 设置服务器信息

![pycharm远程配置4.png](/assets/工具/pycharm远程配置/pycharm远程配置4.png)

# 5. 测试服务器连接是否可用

![pycharm远程配置5.png](/assets/工具/pycharm远程配置/pycharm远程配置5.png)

# 6. 设置上传代码的目录映射

![pycharm远程配置6.png](/assets/工具/pycharm远程配置/pycharm远程配置6.png)

# 7. 打开设置，设置远程Python解释器

![pycharm远程配置7.png](/assets/工具/pycharm远程配置/pycharm远程配置7.png)

# 8. 选择已存在的服务器设置

![pycharm远程配置8.png](/assets/工具/pycharm远程配置/pycharm远程配置8.png)

# 9. 选择 Create 复制服务器设置到解释器中

![pycharm远程配置9.png](/assets/工具/pycharm远程配置/pycharm远程配置9.png)

![pycharm远程配置10.png](/assets/工具/pycharm远程配置/pycharm远程配置10.png)

# 10. 选择远程服务器中虚拟环境里的解释器

```
/home/python/.virtualenvs/toutiao/bin/python
```

![pycharm远程配置11.png](/assets/工具/pycharm远程配置/pycharm远程配置11.png)

# 11. 设置远程服务器运行代码的目录映射

![pycharm远程配置12.png](/assets/工具/pycharm远程配置/pycharm远程配置12.png)

# 12. 若 Pycharm 不能自动上传代码，可勾选 Automatic Upload

![pycharm远程配置13.png](/assets/工具/pycharm远程配置/pycharm远程配置13.png)
