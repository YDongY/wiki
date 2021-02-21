---
title: Ubuntu 启动错误解决
description: 
published: true
<<<<<<< HEAD
date: 2021-02-21T13:34:39.072Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:35.716Z
=======
date: 2021-02-21T14:36:50.464Z
tags: 
editor: undefined
dateCreated: 2021-02-21T14:36:48.047Z
>>>>>>> docs: add all untracked content
---

# Ubuntu 启动错误

经过自己多次瞎整，各种升级，导致 `/boot` 空间不足，然后按照网站一些教程清理内核，导致开机出现了引导问题，无法正确加载内核。

> Kernel panic - not syncing VFS unable to mount root fs on unknown-block 0 0
{.is-danger}

#  解决方案

当出现上诉问题时，再次开机启动，会弹出开启启动选项，此时需要选择 `Ubuntu 高级选项` ，里面有已经安装的多个内核版本。选择一个不带 `recovery mode` 的 `linux-image`。

如果选择内核还是无法进入系统桌面，就需要挨个尝试。成功进入桌面后，先通过命令查看当前正常使用的内核：

```bash
$ uname -a
Linux ydongy-ubuntu 5.8.0-34-generic #37~20.04.2-Ubuntu SMP Thu Dec 17 14:53:00 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

然后通过以下命令查看并删除旧内核

- 查看

```bash
$ dpkg --get-selections | grep linux
```

- 删除

```bash
sudo apt-get purge linux-image-*.*.* # 注意不要选择当前使用的内核版本
sudo apt-get purge linux-headers-*.*.*
sudo apt-get purge linux-modules-*.*.*
```

删除完成之后，可以再次查看

```bash
$ dpkg --get-selections | grep linux
```

如果发现已经删除的 `linux-headers` 、`linux-modules` 依然存在，但是其状态为 `deinstall` 可以通过下面命令删除

```bash
sudo dpkg -P linux-image-*.*.*
sudo dpkg -P linux-headers-*.*.*
sudo dpkg -P linux-modules-*.*.*
```

最后把 `grub2` 更新，然后重启系统

```bash
sudo update-grub2
sudo reboot
```
