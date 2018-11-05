---
title: Git常用命令整理
categories: 版本控制之Git
tags:
  - Git
  - Bash
date: 2018-04-24 10:25:17
---

## 前言碎碎念

自从使用Git作为版本控制工具以来，通过教程学习、手册查阅方式了解了Git的相关原理和Git的命令，能够顺利使用。但由于还不熟练，实践经验也还不够丰富，每次遇到问题都需要重新搜索，多次下来十分麻烦。另一方面，查阅手册往往是不够的，因为手册只会告诉你什么命令做什么用，不会根据不同场景告诉你应该用什么命令。

所以在这篇文章中，我将常用的Git命令根据不同的使用场景做一个整理，加深印象的同时也方便自己日后进行查阅。<!--more-->

***

## 四个概念

这里借用阮一峰老师的文章[《常用Git命令清单》](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)中的图。
![Git](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Git%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E6%95%B4%E7%90%86/Git_common_command.png)

几个专有名词的译名如下：

* Workspace: 工作区，也就是正在编辑的文件目录
* Index / Stage: 暂存区
* Repository: 本地仓库，.git文件夹管理的版本库
* Remote: 远程仓库，也就是github.com上面的仓库

例如，在最常用的命令串中：

``` bash
$ git add <file>
#添加工作区指定文件的改动到暂存区，"<file>"为"."时添加全部文件

$ git commit -m "XXXX"
#提交暂存区的所有内容到本地仓库的当前分支

$ git push -u origin master
#上传本地仓库到已关联的远程仓库
```

---

## 建立工程

在工作目录中建立与远程仓库关联的`Git`工程主要有两种情况：第一种是由本地上传到远程仓库；第二种是从远程仓库克隆到本地。

### 本地上传

在这种情况下，远程仓库应该是没有工程的。在本地工程文件夹下：

``` bash
$ git init
#初始化一个Git仓库，此时当前目录会增加一个.git文件夹(此文件夹默认是隐藏的)，当前文件夹受到Git的管理，并默认创建master分支

$ git add <file>
#添加指定文件到暂存区，"<file>"为"."时表明添加当前目录的所有文件到暂存区

$ git commit -m "XXXX"
#提交暂存区的所有内容到本地仓库的当前分支

$ git remote add origin <url>
#为当前项目添加远程主机。
#其中origin为自定义的远程主机名，url为远程主机的地址（推荐采用ssh协议）
```

此时已经建立了本地仓库与远程仓库的关联，可以通过`git push`推送上传。
第一次推送采用：

``` bash
$ git push -u origin master
#将本地master分支推送到远程同名分支（若不存在则新建），同时-u指定origin为默认主机名，之后若要上传到origin可省略它。
```

### 远程克隆

这种情况下，远程仓库已经有工程，只需要在本地工程文件夹下用`git clone`命令克隆：

``` bash
$ git clone <url>
```

此时本地仓库已经与对应远程仓库建立关联，为主机名`origin`的地址。

### 克隆其他分支

`git clone`命令默认克隆远程项目的`master`分支及其历史，若还需克隆别的分支，可通过以下方式进行（以克隆`dev`分支为例）：

``` bash
$ git checkout -b dev origin/dev
#检出origin下的dev分支到本地新建的dev分支，并建立本地分支与远程分支的追踪关系
```

或者：

``` bash
$ git checkout -b dev
#新建并切换到本地分支dev

$ git branch --set-upstream-to=origin/dev dev
#建立origin/dev远程分支和dev本地分支的追踪关系

$ git pull
#拉取本地分支dev对应的远程分支的最新状态
```

### 托管到新的远程仓库

在克隆需要的内容后，有时会希望托管到新的远程仓库。
此时可以增加新的远程主机名:

``` bash
$ git remote add <new_remote_name> <url>
```

或者干脆更改原来`origin`的地址：

``` bash
$ git remote origin set-url <url>
```

---

## 分支管理

### 查看分支

``` bash
$ git branch
#查看本地分支

$ git branch -r
#查看远程分支

$ git branch -a
#查看所有本地分支和远程分支
```

### 新建本地分支

新建分支（不切换）：

``` bash
$ git branch <new_branch>
```

新建分支并切换到新分支：

``` bash
$ git checkout -b <new_branch>
#相当于：
$ git branch <new_branch>
$ git checkout <new_branch>
```

### 删除本地分支

``` bash
$ git branch -d <branch>
#删除分支前检查该分支是否有未提交或者未合并的内容

$ git branch -D <branch>
#强制删除该分支
```

### 新建远程分支

相当于把远程未添加的本地分支push到远程：

``` bash
$ git push origin <local_branch>:<remote_branch>
#建议远程与本地分支同名，同名时可省略远程分支名
```

### 删除远程分支

相当于`push`一个本地的空分支：

``` bash
$ git push origin :<remote_branch>
#本地分支为空
```

或者用`--delete`：

``` bash
$ git push origin --delete <remote_branch>
```

### 合并分支

``` bash
$ git merge <branch>
#快进合并（指针指向改变），合并<branch>分支到当前分支

$ git merge --no-ff <branch>
#合并<branch>到当前分支，在当前分支生成新节点，保证每个分支的独立演变史
```

---

## 撤销与版本回退

### 撤销工作区修改

有时修改工作区后，发现修改错误，希望回到原来未修改时（上一次提交或暂存）的状态。可以采用`git checkout`命令：

``` bash
$ git diff
#查看工作区未提交（或为暂存）的文件的具体修改

$ git checkout -- <file>
#恢复工作区指定文件到上一次提交（或暂存）状态
$ git checkout .
#撤销所有工作区修改
```

### 撤销暂存区修改

``` bash
$ git reset HEAD <file>
# 将指定文件撤出暂存区

$ git checkout -- <file>
# 将文件在工作区的修改也撤销
```

### 版本回退

希望将版本库回退到之前的提交时，采用`git reset`命令：

``` bash
$ git log
#查看之前的版本提交记录

$ git reflog
#记录你的每一条命令，用于查看命令历史

$ git reset --hard HEAD^
#回退到上一个提交版本，^^代表上两个版本，以此类推。（也可以用~2等代替）
或
$ git reset --hard <commitID>
#commitID可由git log查看得到
```

有必要整理一下`git reset`命令的三个参数：

``` bash
$ git reset --soft HEAD^
#重置版本库头指针，且将这次提交之后的所有变更移动到暂存区

$ git reset --mixed HEAD^
#默认参数，等同于 git reset HEAD^
#重置版本库头指针和暂存区，即这次提交之后的所有更改都留在工作区

$ git reset --hard HEAD^
#重置版本库头指针、暂存区和工作区，即这次提交之后的所有更改都不在存在于当前状态
```

在没有将之后的提交推送到远程仓库的情况下，`git reset --hard`是个很危险的操作。若是已经推送到远程仓库，使用`git pull`可以重新获得之后的版本提交。
若是在没有远程备份时使用`--hard`进行版本回退，又想恢复到之后的版本，在一定时间内（一般为30天）可以通过`git reflog`查看操作id，再使用`git reset --hard <ID>`恢复。

### 删除文件

在`Git`中，删除也是一个修改操作，来让我们实战一下

```bash
$ rm <file>
# 在工作区删除该文件

$ git rm <file>
# git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容

$ git commit -m "rm xxx"
# 向版本库提交信息
```

### stash储藏

有时手头的工作进行到一半，需要切换分支做一些其他事情，可以采用`git stash`命令将当前的工作区储藏起来。

``` bash
$ git stash
#储藏当前工作区

$ git stash list
#查看当前的stash储藏栈

$ git stash apply
#应用栈顶的储藏内容，恢复工作区到之前的储藏状态

$ git stash apply stash@{2}
#应用指定储藏内容

$ git stash pop
#与apply类似，但从栈中删除该储藏内容
```

---

## 多人协作

### 推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```bash
#默认推送的为master主分支
$ git push origin master

#如果要推送其他分支，将该分支对应的名字来替换master即可
$ git push origin dev
```

### 抓取分支

多人协作时，大家都会往`master`和`dev`分支上推送各自的修改。
当你的小伙伴从远程库`clone`时，默认情况下，你的小伙伴只能看到本地的`master`分支。不信可以用`git branch`命令看看：

```bash
$ git branch
* master
```

现在，你的小伙伴要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是他用这个命令创建本地`dev`分支：

```bash
#注意：本地分支名称最好与远程分支保持一致。
$ git checkout -b dev origin/dev
```

现在，他就可以在`dev`上继续修改，然后，时不时地把`dev`分支`push`到远程。

你的小伙伴已经向`origin/dev`分支推送了他的提交，而碰巧你对同样的文件也做了修改，并且试图推送。

这个时候会推送失败,因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单: 先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送。

```bash
$ git pull
```

如果`git pull`提示`“no tracking information”`，则说明本地分支和远程分支的链接关系没有创建，用命令

```bash
$ git branch --set-upstream branch-name origin/branch-name
```

最后再将你改好的解决冲突之后的分支`push`到远程仓库。
这就是多人协作的工作模式，一旦熟悉了，就非常简单。

### rebase

从前面的分析我们可以看出，多人在同一个分支上协作时，很容易出现冲突。即使没有冲突，后`push`的童鞋不得不先`pull`，在本地合并，然后才能`push`成功

这样每次合并再`push`以后，分支看上去会很乱。有强迫症的童鞋会问：为什么Git的提交历史不能是一条干净的直线？

其实是可以做到的！`Git`有一种称为`rebase`的操作，有人把它翻译成<b>“变基”</b>
`rebase`操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了

```bash
$ git rebase
# rebase操作可以把本地未push的分叉提交历史整理成直线；
# rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比
```

---

## 其他

### 状态查看

``` bash
$ git status
```

任何情况下都可以使用`git status`命令查看当前的版本控制状态（包括工作区、暂存区、仓库区），并给出当前状态下可能会用到的命令提示。
经常使用该命令是好习惯。

### 查看历史信息

```bash
$ git log [--pretty=oneline]
# git log命令显示从最近到最远的提交日志
```

### 查看远程库信息

```bash
$ git remote -v
```

### 配置git用户

``` bash
$ git config user.name "your name"
$ git config user.email "email@example.com"
# 配置当前目录的git用户，加上--config参数时配置这台机器的所有git仓库
```

### 协议更改

有时版本克隆是采用的是https协议，以至于每一次提交都需要输入用户名密码，很麻烦。而使用ssh协议就会方便很多，需要将当前的仓库协议进行更换。
事实上，重置远程仓库名为ssh协议地址就可以了。

``` bash
$ git remote origin set-url git@example.com....
```


