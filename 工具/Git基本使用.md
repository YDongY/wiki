---
title: Git 基本使用
description: 
published: true
date: 2021-02-21T13:34:22.279Z
tags: 
editor: undefined
dateCreated: 2021-02-21T08:35:11.573Z
---

## 1. Ubuntu 安装

[下载安装](https://git-scm.com/book/zh/v2)

```shell
sudo apt-get install git
```

## 2. Git 常用命令

### `git config`

- 设置
  - `--local`：只对某个仓库有效
  - `--global`：对当前用户所有仓库有效
  - `--system`：对系统所有登录的用户有效

```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
# 中文乱码
git config --global core.quotepath false
```

- 查看

```shell
git config --list --local
git config --list --global
git config --list --system
```

### `git init`

```shell
# 在当前指定目录下创建
git init

# 指定目录新建一个仓库
git init [project-name]
```

### `git add`

```shell
# 添加所有
git add .

# 添加指定文件
git add xx/xx/xx
```

### `git commit`

```shell
# 提交暂存区到仓库区
git commit -m "描述（方便日后查看回忆此次提交内容）"

# 跳过 add ，直接提交
git commit -a -m "meaasge"

# 提交时显示所有diff信息
git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次 commit 的提交信息
git commit --amend -m [message]
```

### `git mv`

传统方式重命名会给 Git 识别为删除和新增两个步骤，使用下面的命令可以简化

```shell
git mv oldname newname

# 等价于
# $ mv README.md README
# $ git rm README.md
# $ git add README
```

### `git rm`

```shell
# 删除工作区文件
git rm [filename/glob]

# 删除暂存区文件，强制删除，不可恢复
git rm -f [filename/glob]

# 删除暂存区文件保留本地
git rm --cached [filename/glob]
```

### `git diff`

```shell
# 对比两次 commit 的不同之处
git diff [commit_id/name] [commit_id/name] -- filename

# 暂存区和 HEAD 指针比较
git diff --cached/staged

# 工作区和暂存区比较
git diff
# 指定文件
git diff -- filename ...

# 找到可能的空白错误并将它们为你列出来
git diff --check

# 显示今天你写了多少行代码
git diff --shortstat "@{0 day ago}"
```

### `git reset`

```shell
# 恢复暂存区所有内容
git reset HEAD

# 恢复暂存区指定文件
git reset HEAD -- filename

# 版本回退
git reset --hard commit_id
```

### `git restore`

**丢弃变更**：当修改的文件已经存在暂存区，当我们再次修改以后想要丢弃本次修改，可以通过以下命令恢复暂存区状态

> 注意：执行该命令未保存在暂存区的修改都会丢弃，无法找回

```shell
git checkout -- filename
git restore filename
```

```shell
# 取消暂存区变更
git restore --staged filename
```

### `git checkout`

```shell
# 切换分支
git checout <name>

# 创建分支并且换到分支
git checkout -b <name>
```

### `git rebase`

```shell
$ git rebase -i commit_id

# 这里会显示 commit_id 之后的所有 commit 信息
pick cf4046b feat:add Git 基本使用
# Rebase 194f6a4..c3fbf03 onto 194f6a4 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

- 修改老旧 `commit` 的 `message`

执行命令 `git rebase -i [前一个 commit_id]`，然后把 `pick` 改为 `reworld`，保存之后修改提交信息

- 合并多个连续 `commit`

```shell
# 这个顺序是倒叙，下面的是更新的提交
pick 9c63781f add index.html
pick 8d6738g2 add style.css  ======>     squash 8d6738g2 add style.css
pick 7f653e13 add js.js      ======>     squash pick 7f653e13 add js
pick fead4637 add README
```

意思就是想要合并前三个 `commit` 为一个。把 `pick` 修改为 `squash` 之后保存，然后会弹出另一个界面，填写最终的 `message` 即可。 

- 合并多个不连续的 `commit`

执行命令 `git rebase -i [最早提交的前一个 commit_id]`，然后修改 `pick` 为 `squash`。例如把下列三个 `.py` 文件合并为一个 `commit`：

```shell
pick b81014d feat:add world.py &  hello.py
pick 0b04ded feat:add test.py
pick 51aea6e feat:add a.txt  
pick 97b0e96 feat:add b.txt
pick c65a223 feat:add ttt.py # 移动此 py 文件
```

只需要把需要合并的 `commit` 放在一起，修改如下：

```shell
pick b81014d feat:add world.py &  hello.py
squash 0b04ded feat:add test.py # 修改 pick 为 squash
squash c65a223 feat:add ttt.py  # 修改 pick 为 squash
pick 51aea6e feat:add a.txt  
pick 97b0e96 feat:add b.txt
```

> 在合并多个 `commit` 时，如果把第一个提交的 `commit` 也做了修改，就会新产生一条 `git` 主线。

### `git log`

| 参数           | 说明                   |
| -------------- | ---------------------- |
| `--oneline`    | 简化查看               |
| `-n[num]`      | 指定最近 n 个 commit   |
| `--stat`       | 每次提交的简略统计信息 |
| `-S [keyword]` | 根据关键词搜索提交历史 |
| `--all`        | 包含所有分支的 commit  |
| `--graph`      | 图形化                 |
| `-p`           | 每次提交的差异         |
| `--no-merges`  | 格式化的提交信息       |

### `git branch`

| 参数                   | 说明                                             |
| ---------------------- | ------------------------------------------------ |
| `-a`                   | 查看所有的分支                                   |
| `-v`                   | 查看分支及最后一次提交                           |
| `-d`                   | 删除分支                                         |
| `--merged/--no-merged` | 过滤这个列表中已经合并或尚未合并到当前分支的分支 |

### `git show`

```shell
# 显示所有提交过的用户，按提交次数排序
git shortlog -sn

# 显示某次提交的元数据和内容变化
git show [commit]

# 显示某次提交发生变化的文件
git show --name-only [commit]

# 显示某次提交时，某个文件的内容
git show [commit]:[filename]

# 显示当前分支的最近几次提交
git reflog
```

### `git merge`

```shell
# 把 dev 分支的工作成果合并到 master 分支上
git merge dev
```

### `git clone`

Git 的 `clone` 命令会为你自动将其命名为 `origin`，拉取它的所有数据， 创建一个指向它的 master 分支的指针，并且在本地将其命名为 `origin/master`， 同时 Git 也会给你一个与 origin 的 `master` 分支在指向同一个地方的本地 `master` 分支，这样你就有工作的基础。

![git-remote-branches-1](git-remote-branches-1.png "git-remote-branches-1")

> 远程仓库名字  `origin` 与分支名字 `master` 一样，在 Git 中并没有任何特别的含义一样， `master` 是当你运行 `git init` 时默认的起始分支名字， `origin` 是当你运行 `git clone` 时默认的远程仓库名字

可以自定义远程仓库名字：运行 `git clone -o ydongy`，那么你默认的远程分支名字将会是 `ydongy/master`。

```shell
git clone -o name
```

### `git fetch`

如果要与给定的远程仓库同步数据，运行 `git fetch <remote>` 命令， 这个命令查找 `origin` 是哪一个服务器，从中抓取本地没有的数据，并且更新本地数据库，移动 `origin/master` 指针到更新之后的位置。

![git-remote-branches-3](git-remote-branches-3.png "git-remote-branches-3")

当抓取到新的远程跟踪分支`origin/dev`时，本地不会自动生成一份可编辑的副本（拷贝），也就是说不会有一个新的 `dev` 分支，只有一个不可以修改的 `origin/dev` 指针

可以运行 `git merge origin/dev` 将这些工作合并到当前所在的分支。 如果想要在自己的 `dev` 分支上工作，可以将其建立在远程跟踪分支之上：

```shell
git checkout -b dev origin/dev
```

### `git push`

```shell
# 上传本地指定分支到远程仓库
git push [remote] [branch]

git push origin [local_branch]:[remote_branch]
# git push origin dev:dev 推送本地的 def 分支，将其作为远程仓库的 def 分支
# git push origin dev:dev2 将本地的 def 分支推送到远程仓库上的 dev2 分支

# 强行推送当前分支到远程仓库，即使有冲突
git push [remote] --force

# 推送所有分支到远程仓库
git push [remote] --all

# 删除远程分支
git push origin --delete xxx
```

### `git remote`

```shell
# 列出已经存在的远程分支
git remote

# 详细信息
# 在每一个名字后面列出其远程url，此时， -v 选项(–verbose)，显示对应的克隆地址。
git remote -v | --verbose

# 添加一个远程仓库
# git remote add dev git://github.com/CnPeng/test.git 这样
# 就可以用字符串 dev 来代指该仓库地址。在使用 git remote -v 查看仓库列表的时候，就会有一个 dev 的仓库列表。
git remote add [shortname] [url]

# 查看远程仓库信息
git remote show [shortname]

# 重命名远程仓库
git remote rename [old-name] [new-name]

# 删除远程仓库（解除与远程仓库的关系）
git remote rm [shortname]
```

### `git pull`

```shell
git pull <远程主机名> <远程分支名>:<本地分支名>

# 取回origin主机的next分支，与本地的master分支合并，需要写成下面这样。
git pull origin next:master

# 如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
git pull origin next

# 上面命令表示，取回origin/next分支，再与当前分支合并。实质上，这等同于先做git fetch，再做git merge。
git fetch origin
git merge origin/next
```

> 在某些场合，Git 会自动在本地分支与远程分支之间，建立一种**追踪关系**(tracking)。比如，在 `git clone` 的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的 `master` 分支自动追踪 `origin/master `分支。

```shell
# 跟踪分支
git checkout -b <branch> <remote>/<branch>
git checkout --track origin/dev # 默认本地创建一个 dev 分支，跟踪远程 dev 分支

# 跟踪不同名字
git checkout -b dev origin/dev2 # 本地分支 dev 会自动从 origin/dev2 拉取。

#　手动建立追踪关系。
# 指定master分支追踪origin/next分支。
git branch --set-upstream master origin/next

# 如果当前分支与远程分支存在追踪关系，git pull就可以省略远程分支名
git pull origin
```

### `git stash`

```shell
# 将当前分支存起来
git stash

git stash save "message"

# 查看我们“存储”的列表
git stash list

# 恢复现场,恢复后，stash 内容并不删除
git stash apply

# 恢复的同时把 stash 存储列表的内容也删了
git stash pop

# 删除
git stash drop
```

> 如果有一个分支上多个 stash，如果需要恢复指定的 stash ，可以在命令尾部加 `id`，如 `$ git stash apply stash@ {0}`，同样删除指定 stash 项目则执行如 `$ git stash drop stash@{1}`

### `git tag`

- 创建标签

```shell
#列出标签
git tag

# 创建标签
# 附注标签
git tag -a v1.4 -m "my version 1.4"
git show

# 轻量标签
git tag v1.4-lw

# 后期打标签
git reflog
git tag -a v1.2 9fceb02
```

- 推送标签

```shell
# 共享标签
# 默认情况下，git push 并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库

# 推送一个标签
git push origin v1.5

# 推送所有本地新增的标签上去
git push origin --tags

# 跳转到指定标签
git checkout v0.0.1
```

- 删除标签

```shell
# 删除本地指定标签
git tag -d v0.0.1

# 删除远程标签
git push origin -d tag <tagname>
```

### `git submodule`

将一个已存在的 Git 仓库添加子模块，同时默认会创建新的 `.gitmodules` 文件。 该配置文件保存了项目 URL 与已经拉取的本地目录之间的映射：

```shell
git submodule add [url]
```

当我们在克隆包含子模块项目时，默认会包含该子模块目录，但其中还没有任何文件。不过可以运行两个命令：`git submodule init` 用来初始化本地配置文件，而 `git submodule update` 则从该项目中抓取所有数据并检出父项目中列出的合适的提交。

或者使用以下命令，自动初始化并更新仓库中的每一个子模块， 包括可能存在的嵌套子模块。

```shell
git clone --recurse-submodules [url]
```

如果子模块有新的提交，但是不想在子目录中手动抓取与合并，可以执行下面命令

```shell
git submodule update --remote [submodule_name]
```

此命令默认会假定你想要更新并检出子模块仓库的 `master` 分支，不过你也可以设置为想要的其他分支

```shell
git config -f .gitmodules submodule.[submodule_name].branch [branch_name]
```

## 3. Git 别名

```shell
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

## 4.  Git 是如何保存数据的

```shell
$ ls -al
总用量 20
drwxrwxr-x 4 ydongy ydongy 4096 12月 19 13:36 .
drwxrwxr-x 5 ydongy ydongy 4096 12月 19 13:34 ..
drwxrwxr-x 8 ydongy ydongy 4096 12月 19 13:37 .git
drwxrwxr-x 2 ydongy ydongy 4096 12月 19 13:36 images
-rw-rw-r-- 1 ydongy ydongy   19 12月 19 13:36 README.md
```

```shell
$ cd .git
$ ll     
总用量 44K
drwxrwxr-x 2 ydongy ydongy 4.0K 12月 19 13:34 branches
-rw-rw-r-- 1 ydongy ydongy   29 12月 19 13:37 COMMIT_EDITMSG
-rw-rw-r-- 1 ydongy ydongy   92 12月 19 13:34 config
-rw-rw-r-- 1 ydongy ydongy   73 12月 19 13:34 description
-rw-rw-r-- 1 ydongy ydongy   23 12月 19 13:34 HEAD
drwxrwxr-x 2 ydongy ydongy 4.0K 12月 19 13:34 hooks
-rw-rw-r-- 1 ydongy ydongy  256 12月 19 13:37 index
drwxrwxr-x 2 ydongy ydongy 4.0K 12月 19 13:34 info
drwxrwxr-x 3 ydongy ydongy 4.0K 12月 19 13:37 logs
drwxrwxr-x 9 ydongy ydongy 4.0K 12月 19 13:37 objects
drwxrwxr-x 4 ydongy ydongy 4.0K 12月 19 13:34 refs
```

目录中最重要的就是 `objects`，里面存放 Git 管理的所有内容：

```shell
cd objects
$ ls
4d  7e  8a  a0  d0  info  pack
```

进入目录之后每个文件名类似于`commit id`，但是我们直接 `cat` 查看内容是乱码的，可通过下面命令查看文件类型：

```shell
#  注意后面的一串是文件夹名+文件名，即 4d + 71f400aafab4b9c088593be6ee486314b713df
$ git cat-file -t 4d71f400aafab4b9c088593be6ee486314b713df
blob

$ git cat-file -t 7e3263432087a847d4fe6e53b59fb8a93152d91c
tree
```

其中 `tree` 表示目录，`blob` 表示文件，想要查看文件内容可以通过 `git cat-file -p`，总结：

- 如果提交的是文件夹，其对应类型就是 `tree`
- 如果提交的是文件，其对应类型就是 `blob`
- 如果是文件夹中有文件，则文件夹是 `tree`，里面的文件是 `blob`

**假设我们执行一下命令：**

```shell
git add README test.rb LICENSE
git commit -m 'The initial commit of my project'
```

当使用 `git commit` 进行提交操作时，Git 会先计算每一个子目录的校验和（`SHA-1 哈希算法`）， 然后在 Git 仓库中这些校验和保存为**树对象**，随后，Git 便会创建一个提交对象， 它除了包含上面提到的那些信息外，还包含指向这个树对象的指针。

现在，Git 仓库中有五个对象：三个 **blob** 对象（保存着文件快照）、一个 **树** 对象 （记录着目录结构和 blob 对象索引）以及一个 **提交** 对象（包含着指向前述树对象的指针和所有提交信息）。

![git-commit-and-tree](git-commit-and-tree.png "git-commit-and-tree")

当做些修改后再次提交，那么这次产生的提交对象会包含一个指向上次提交对象（父对象）的指针。

![git-commits-and-parents](git-commits-and-parents.png "git-commits-and-parents")

## 5. HEAD 和 Branch 关系

假设当前处于 `master` 分支，想要创建一个 `testing` 分支， 你需要使用 `git branch` 命令：

```shell
git branch testing
```

这会在当前所在的提交对象上创建一个指针。

![git-two-branches](git-two-branches.png "git-two-branches")

Git 又是怎么知道当前在哪一个分支上呢？也很简单，它有一个名为 `HEAD` 的特殊指针，指向当前所在的本地分支

![git-head-to-master](git-head-to-master.png "git-head-to-master")

现在我们想要使用 `git checkout` 命令切换到新创建的 `testing` 分支去：

```shell
git checkout testing
```

这样 `HEAD` 就指向 `testing` 分支了。

![git-head-to-testing](git-head-to-testing.png "git-head-to-testing")

此时我们又再一次的提交了内容：

```shell
vim test.rb
git commit -a -m 'made a change'
```

HEAD 分支随着提交操作自动向前移动

![git-advance-testing](git-advance-testing.png "git-advance-testing")

如图所示， `testing` 分支向前移动了，但是 `master` 分支却没有，它仍然指向运行 `git checkout` 时所指的对象。 这就有意思了，现在我们切换回 `master` 分支看看：

```shell
git checkout master
```

![git-checkout-master](git-checkout-master.png "git-checkout-master")

这条命令做了两件事：

1. HEAD 指回 `master` 分支
2. 将工作目录恢复成 `master` 分支所指向的快照内容

此时我们处于 `master` 分支，同样我们也进行了修改并提交，则提交历史已经产生了分叉

![git-advance-master](git-advance-master.png "git-advance-master")

随着不同分支间不断地来回切换和工作，并在时机成熟时将它们合并起来。

## 6. 什么是分离头指针

所谓分离头指针，就是直接通过 `git checkout [commit_id]` 来修改 `HEAD` 的指向，导致`HEAD` 不会标记任意一个 `branch` 。 当我们在分离头指针的位置上进行内容修改之后，想要切换分支，此时会提示一个错误，要求必须为当前分离头指针创建一个 `branch` ，否则直接 `checkout` 不做任何处理，会导致处于分离头指针进行的任何操作都会被清掉

```shell
git branch <新分支名>
```

由此我们看出来，分支头指针就是 `HEAD` 作用在任意一个 `commit` ，而任意一个分离头指针想要存活下就必须建立对应的 `branch`，而一个 `branch` 本质就是对应了一系列 `commit` 中最新的一次 `commit`。总而言之：`HEAD` 和 `Branch` 都可对应某次 `commit`，而一般它们处于同一个且是最新的 `commit`。

## 7. hotfix 分支的使用

存在以下场景：

1. 处于 `iss` 分支开发新功能

```shell
git checkout -b iss53
```

![git-basic-branching-2](git-basic-branching-2.png "git-basic-branching-2")

```shell
vim index.html
git commit -a -m 'added a new footer [issue 53]'
```

![git-basic-branching-3](git-basic-branching-3.png "git-basic-branching-3")

2. 突然有个很严重的问题需要紧急修补，保存现场

```shell
git stash
```

3. 切换到线上分支

```shell
git checkout master
```

4. 为这个紧急任务新建一个分支 `hotfix`，并在其中修复它

```shell
git checkout -b hotfix
vim index.html
git commit -a -m 'fixed the broken email address'
```

![git-basic-branching-4](git-basic-branching-4.png "git-basic-branching-4")

5. 在测试通过之后，切换回线上分支，然后合并 `hotfix` 分支，最后将改动推送到线上分支。

```shell
git checkout master
git merge hotfix
```

![git-basic-branching-5](git-basic-branching-5.png "git-basic-branching-5")

6. 删除 `hotfix` 并切换回 `iss` 分支上，继续工作

```shell
git branch -d hotfix
git checkout iss53
```

![git-basic-branching-6](git-basic-branching-6.png "git-basic-branching-6")

但是在 `hotfix` 分支的工作还没有包含在 `iss` 分支，所以需要拉取 `hotfix` 所做的修改，同时还要恢复工作区内容：

```shell
git merge master
git stash [apply/pop]
```

当我们的 `iss` 分支开发完毕，也需要合并到 `master` 分支:

```shell
git checkout master
git merge iss
```

![git-basic-merging-2](git-basic-merging-2.png "git-basic-merging-2")

7. 最后删除 `iss` 分支

```shell
git branch -d iss53
```

## 8. .gitigonre

```shell
echo "function gi() { curl -sLw "\n" https://www.toptal.com/developers/gitignore/api/\$@ ;}" >> ~/.zshrc && source ~/.zshrc
gi python > .gitignore
```

## 9. 协议

### 本地协议

远程版本库就是同一主机上的另一个目录。克隆一个本地版本库，可以执行如下的命令：

```shell
git clone /srv/git/project.git
git clone file:///srv/git/project.git
```

增加一个本地版本库到现有的 Git 项目，可以执行如下的命令：

```shell
git remote add local_proj /srv/git/project.git
```

然后，就可以通过新的远程仓库名 `local_proj` 像在网络上一样从远端版本库推送和拉取更新了

### HTTP 协议

```shell
git clone https://example.com/gitproject.git
git clone git://example.com/gitproject.git
```

### SSH 协议

通过 SSH 协议克隆版本库，你可以指定一个 `ssh://` 的 URL：

```shell
git clone ssh://[user@]server/project.git
git clone [user@]server:project.git
```

### 生成 SSH 公钥

```shell
ssh-keygen -t rsa -C "邮箱地址"
```

密钥的存储位置（默认是` .ssh/id_rsa`）





## 参考

- https://git.apachecn.org/
- [Git 在线练习闯关]( https://learngitbranching.js.org/?locale=zh_CN)
- [Git 官网](https://git-scm.com/book/zh/v2)
- [.gitigonre](https://github.com/github/gitignore)
- [gitignore.io](https://www.toptal.com/developers/gitignore)