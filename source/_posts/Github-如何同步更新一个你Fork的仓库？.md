---
title: Github如何同步更新一个你Fork的仓库？
categories: 版本控制之Git
tags: Git
date: 2018-06-12 22:14:56
---

> 我们在进行Github协同开发的时候，往往会去fork一个仓库到自己的Github中，过一段时间以后，原仓库可能会有各种提交以及修改，很可惜，Github本身并没有自动进行同步的机制，这个需要我们手动去执行，现在我来演示一下如何进行自己的仓库和原仓库进行Gith同步的操作。重点在于：除了origin以外，增加一个upstream，来跟踪原仓库的更新。
<!--more-->

---

## 先将你`fork`到自己的Github的远程仓库克隆到本地
```bash
$ git clone https://github.com/zmzhouXJTU/Problem-sets
```

## 查看远程仓库的信息

```bash
$ git remote -v
origin  https://github.com/zmzhouXJTU/Problem-sets (fetch)
origin  https://github.com/zmzhouXJTU/Problem-sets (push)
```

## 配置原远程仓库(即你所`fork`的)的路径

```bash
$ git remote add upstream https://github.com/ACLoong/Problem-sets
```

## 再次查看远程仓库信息确定是否配置成功

```bash
$ git remote -v
origin  https://github.com/zmzhouXJTU/Problem-sets (fetch)
origin  https://github.com/zmzhouXJTU/Problem-sets (push)
upstream        https://github.com/ACLoong/Problem-sets (fetch)
upstream        https://github.com/ACLoong/Problem-sets (push)

```

## 抓取原仓库的更新文件，该文件会被存储在一个本地分支`upstream/master`上

```bash
$ git fetch upstream
remote: Counting objects: 21, done.
remote: Compressing objects: 100% (15/15), done.
remote: Total 21 (delta 4), reused 5 (delta 1), pack-reused 0
Unpacking objects: 100% (21/21), done.
From https://github.com/ACLoong/Problem-sets
 * [new branch]      master     -> upstream/master

```

## 切换到本地主分支(**如果当前不在的话**)

```bash
$ git checkout master
# Switched to branch 'master'
```

## 将原仓库的更新文件(即存储在本地的`upstream/master`分支上的文件)与本地仓库的当前分支合并

```bash
$ git merge upstream/master
Updating 60b537a..426148c
Fast-forward
 problem_set/first_week/refs/Process&Thread.md | 29 +++++++++++++++++++++++++++
 1 file changed, 29 insertions(+)
 create mode 100644 problem_set/first_week/refs/Process&Thread.md
```

**Note:** 此时，你的**本地仓库**已经和**原来仓库**完全同步了。但是注意，此时只是你电脑上的本地仓库和原作者的远程`github仓库`同步了，你`fork`到自己的github远程仓库还没有同步。要想实现本地和自己的Github远程仓库的同步，只需要如下操作即可：

## 将其更新到自己的Github远程仓库，可以利用命令`git push origin master`来实现

```bash
$ git push origin master
Counting objects: 21, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (21/21), 5.65 KiB | 1.88 MiB/s, done.
Total 21 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), completed with 2 local objects.
To https://github.com/zmzhouXJTU/Problem-sets
   60b537a..426148c  master -> master
```
好了，至此任务就完成了。不知你是否看明白了呢？有任何问题，欢迎在此博客下方评论留言。