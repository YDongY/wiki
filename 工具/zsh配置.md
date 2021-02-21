---
title: zsh 配置
description: 
published: true
date: 2021-02-21T13:34:27.531Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:20.101Z
---

# oh My Zsh 安装

[Oh My Zsh](https://ohmyz.sh/) 是基于 zsh 命令行的一个扩展工具集，提供了丰富的扩展功能。除了功能增强之外，还提供非常丰富的主题。使用 Oh-My-Zsh 打造酷炫 Shell 终端的步骤：

- 下载

```shell
sudo apt install zsh
```

- 安装

```shell
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

如果出现正在连接 `raw.githubusercontent.com (raw.githubusercontent.com)|0.0.0.0|:443... 拒接连接`。可在 `hosts` 文件添加如下内容：

```shell
# sudo vim /etc/hosts
151.101.76.133 raw.githubusercontent.com
```

- 设置为默认（安装完成会提示），选择 y 不用执行以下命令。

```shell
chsh -s $(which zsh)
```

> 设置完成以后，注销重启生效

- [主题预览](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)

```shell
vim ~/.zshrc

# ----修改
ZSH_THEME="ys"

source ~/.zshrc
```

# 实用插件安装

## zsh-autosuggestions

会记录是已经输入的命令，下次再次输入会自动匹配你可能想要输入的命令

```shell
cd ~/.oh-my-zsh/custom/plugins/
git clone https://github.com/zsh-users/zsh-autosuggestions
```

## zsh-syntax-highlighting

高亮显示命令，同时会对输入的命令进行检错，错误红色，正确绿色

```shell
cd ~/.oh-my-zsh/custom/plugins
git clone git://github.com/zsh-users/zsh-syntax-highlighting.git
```

## z

oh-my-zsh 默认插件，可实现快速目录切换

## extract

oh-my-zsh 默认插件，万能解压缩命令，可以处理任意格式的压缩包

# 插件设置

```shell
vim ~/.zshrc
# 修改内容，默认只有一个 git
plugins=(git zsh-syntax-highlighting zsh-autosuggestions extract z)
```