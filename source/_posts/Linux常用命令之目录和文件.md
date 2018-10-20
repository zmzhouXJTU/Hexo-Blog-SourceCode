---
title: Linux常用命令
categories: Linux操作系统
tags: Linux
date: 2018-04-26 09:45:24
---

作为一名程序员，相信大家对Linux操作系统都不会陌生。但是Linux系统那些繁杂的操作命令通常让我们很头疼，我也在网上查阅了很多资料，做了一些总结。这里将常用的一些Linux操作命令(主要是与文件相关的操作命令)整理如下：<!--more-->

---

## sudo

这个SuperUserDo(简写成"sudo")是Linux新手要使用的最重要的命令。需要根权限的每一个命令都需要这个`sudo`命令。你可以在需要根权限的每个命令之前使用`sudo`

``` bash
$ sudo su
```

---

## pwd

``` bash
~/Documents$ pwd
# 获取当前所在的路径(绝对路径)
```

---

## ls(list)

借助`ls`(list)命令，终端就会显示你正在处理的那个目录里面的所有文件和文件夹。假设我在Home目录(也就是"~")里面，想查看当前目录下的文件夹和文件。相应的命令如下图所示：

``` bash
~$ ls
# 显示当前目录的所有文件和文件夹
```

上面是`ls`命令最基本的使用方式，下面我们再看看`ls`命令其他的使用方式。

``` bash
~$ ls -l
# 输出详细信息 -l (long 的简写). 这个指令会打印出文件的权限 (-rw-rw-r-- 之后我们在细说这个), 用户名, 文件大小, 修改日期, 文件名
```

``` bash
~$ ls -a
# -a (all 的简写) 显示所有文件 . 这里还会显示隐藏的文件 (以 . 开头的)
```

``` bash
~$ ls -lh
# -lh (human), 直接 -l 不方便人看, 这个指令是为了方便给人观看的. 注意这里的文件大小使用了 K, MB, GB 之类概括
```

``` bash
~$ ls --help
# 还有很多其他的功能, 我们可以通过 --help 来查看
```

---

## cd

更改目录(`cd`)是始终在终端中使用的主要命令。它是最基本的Linux命令之一。使用这个命令很简单。只要输入你想要从当前目录进入到的那个文件夹的名称(如果是多层，中间用"/"进行分隔)。如果想要返回上一级，只要将双圆点(..)作为参数。

假设我在Home目录(也就是"~")中，想进入到在Home目录里面的Documents子目录。下面是我可以使用`cd`命令的方法：

``` bash
~$ cd Documents/
# 跳转到对应的Documents目录
```

若果要想返回到上一级目录，只用将双圆点(..)作为相应的参数即可。如下图所示：

``` bash
~/Documents$ cd ..
# 返回上一级目录
```

除了上面两个基本的命令之外，还有一些有趣的，我们来看看。

``` bash
~$ cd Documents/Folder1/
# 去往子文件夹Folder1
```

``` bash
~/Documents/Folder1$ cd -
# 返回你刚刚所在的目录(也就是你的上一条命令所在的目录)
```

``` bash
~/Documents/Folder1$ cd ../../
# 向上返回两次(再往上返回可以依次类推)
```

``` bash
~/Documents/Folder1$ cd ~
# 去往当前用户的主目录
```

---

## touch

`touch` 命令的意思的新建，它的使用很简单。我们先去往 Documents 的文件夹, 里面已经有了 folder1 和 file1, 如果我们想新建一个 file2 使用下面的语句就好(可以指定文件的扩展名)。一个空文件就这样建立好了。(注意：**如果创建的文件不存在，则会创建一个空白文件；如果文件已经存在，可以修改文件的末次修改日期**)

``` bash
~/Documents$  touch file2.txt
# 建立一个文件名为file2的空的文本文档
```

如果你想同时建立多个文件，输入多个文件的名字，以空格分开。

``` bash
~/Documents$  touch file3.txt file4.txt file5.txt
# 同时建立三个空的文本文档(也可以不指定文件的扩展名)
```

---

## tree

`tree` 命令的意思的新建，可以以树状图列出文件目录结构.

``` bash
~/Documents$  tree [目录名]
# 查看当前目录下的文件目录结构
```

``` bash
~/Documents$  tree -d [目录名]
# 只会显示当前目录下的目录结构而不显示文件结构
```

---

## cp

拷贝粘贴是我们为了组织整理文件而需要完成的重要任务。使用`cp`将帮助你从终端拷贝粘贴文件。首先，你确定想要拷贝的那个文件，然后输入目的地位置，即可粘贴文件。`cp` (copy) 是复制文件或者文件夹的指令, 常用的方式是复制 “源文件” 到 “目标文件”。即：

``` bash
~$ cp 源文件 目标文件
```

**注意:** 如果你将文件拷贝到任何新文件都需要根权限的目录，那么你就需要使用`sudo`命令。

下面是`cp`命令的一些常见操作。

``` bash
~/Documents$ cp file1 file1copy
# 将file1复制成file1copy
```

``` bash
~/Documents$ cp -i file1 file1copy
# -i (interactive) 注意: 如果 file1copy 已经存在, 它将会直接覆盖已存在的 file1copy, 如果要避免直接覆盖, 我们在 cp 后面加一个选项。
# 在这句问句后面打上 “Yes”, “Y”, 或者任何大小写形式的 “y” 和 “yes”, 它将进行覆盖操作. 直接回车或者打其他字母, 就会放弃复制这项操作。
```

``` bash
~/Documents$ cp file1 Folder1/
# 将file1复制到文件夹Folder1
```

``` bash
~/Documents$ cp -R Folder1/ Folder2/
# 复制文件夹, 需要加上 -R (recursive)
```

``` bash
~/Documents$ cp file* Folder1/
# 复制多个文件。复制名字部分相同的多个文件到某个文件夹, * 是说"你就找文件名前面是 file 的文件, 后面是什么名字无所谓"
```

``` bash
~/Documents$ cp file2.txt file5.txt Folder1/
# 或者你可以单独选定几个文件, cp 会默认最后一个选项是要复制去的文件夹. 比如把 file2.txt 和 file5.txt 复制去 Folder1/
```

---

## mv

知道了 `cp`, `mv`就好理解多了, 基本是一样的。`mv`是剪切(移动)的命令。`mv`命令可以用来移动**文件**或**目录**，也可以给**文件**或**目录**重命名。
下面是`mv`命令的一些常见操作。

``` bash
~/Documents$ mv file1 Folder1/
# 将file1移动到文件夹Folder1
```

``` bash
~/Documents$ mv -i file1 file1rename
#  重命名文件file1为file1rename。因为移动文件到原始的地点, 但是以不同的文件名。所以这种做法不就是在重命名嘛!(加上`-i`覆盖文件前提示)
```

``` bash
~/Documents$ mv -f file1 Folder2/
#  将文件file1强制移动到Folder2文件夹下。如果目标文件已经存在，不会询问而会直接覆盖。
```

最后还是想要提一句, 如果想要查看使用说明, 直接在指令后面打上 `--help `就能查看.

---

## mkdir

仅仅会更改目录还不全面。有时候，你想要创建一个新的文件夹或子文件夹。可以使用`mkdir`命令来做到这一点。只要在终端中将你的文件夹名称放在`mkdir`命令的后面即可。
`mkdir` (make directory) 就是创建一个文件夹的意思, 使用起来很简单。(注意：**新建目录的名称**不能与**当前目录中已有的目录或文件**同名)

以下是`mkdir`命令的一些常见操作。

``` bash
~/Documents$ mkdir  Folder2/
# 在Documents文件夹下创建一个文件夹Folder2
```

``` bash
~/Documents$ mkdir  Folder2/folder
# 在文件夹Folder2里面再创建一个子文件夹folder
```

``` bash
~/Documents$ mkdir  -p  A/B/C/D
# 可以递归的创建目录，此处创建了4个目录
```

---

## rmdir

`rmdir `(remove directory) 也就是字面的意思，即移除文件夹。**不过这有一个前提条件. 这些要移除的文件夹必须是空的，不然会失败**。所以如果想刚刚建立的那个 Folder2 就不能被移除, 因为里面有个 folder 文件夹。那么怎么移除有文件的文件夹呢？这里需要用到我们后面讲的`rm`命令。

要移除个空文件夹, 比如我在新建一个 Folder3, 然后移除。

``` bash
~/Documents$ rmdir  Folder3
# 删除文件夹Folder3(rmdir只能移除空文件夹！！！)
```

---

## rm

`rm`这个命令可以移除你的文件，甚至移除你的目录。如果文件需要根权限才能移除，可以使用`-f`(强制删除)。你还可以使用`-r`来进行递归移除，从而移除你的文件夹。

**注意: 执行了 `rm `以后是不能进行返回操作的, 请确保别执行像这样的操作 `rm /`(或者在根目录下进行`rm -rf *`)，这会清空你的电脑**。

``` bash
~/Documents$ rm  file1
# 删除单个文件filer1
```

``` bash
~/Documents$ rm -i file2
# -i 或 -I 有提示地移除文件 (为了避免误删)
# -i 会每个要移除的文件都进行提示
~/Documents$ rm -I file2 fil3 file4 file5
# -I 超过3个文件才进行提示
```

``` bash
~/Documents$ rm -f file1
# 强制删除，忽略不存在的文件，无需提示
```

``` bash
~/Documents$ rm -r Folder1/
# -r 或 -R (recursively) 用来删文件夹(递归的删除目录下的内容)
# 和 rmdir 不同, rm -r 可以在文件夹中有文件的情况下删除这个文件夹. 比如我的 Folder1 里有 file1 和 file2 两个文件.
```

`rm`命令其他的特点和`cp`命令差不多，比如用带相应的前缀或者后缀(或者不带，这样会清空当前文件夹下所有文件)加'*'来一次性删除多个文件。

---

## nano

`nano `是 linux 的一款文字编辑工具. 我们可以拿它来做最基本的 terminal 端的文本编辑, 甚至可以写代码。下面我们用 `touch` 创建一个 Python 脚本。如果大家不懂 
`Python` 也没关系，你就知道我们可以拿 `nano `来编辑文字或者脚本就好了。

``` bash
~/Documents$ touch  p1.py
# 在Documents目录下创建一个python文件
```

然后用 nano 执行这个 p1.py 文件，如下图所示：

``` bash
~/Documents$ nano  p1.py
# 用nano命令执行这个文件
```

它就会变成一个文本编辑器, 你在里面可以写上一些脚本。
然后按 “Ctrl + x” 来保存和退出。如果提示你保存, 你就按一下 “y” 键, 然后回车, 你的文件就被保存下来了。

接着如果你在 terminal 中输入相关的执行python文件的命令，你就能看到 terminal 执行了你的 python 文件。

---

## cat

作为用户，你常常需要查看来自脚本的一些文档或代码。同样，其中一个Linux基本命令是`cat`命令。它会为你显示文件里面的文本。

`cat `(catenate) 可以用来显示文件内容,创建文件,文件合并, 或者是将某个文件里的内容写入到其他文件里。
`cat`会一次性的显示所有的内容,适合**查看内容较少**的文本文件。详细的操作见下面。

``` bash
~/Documents$ cat -b p1.py 
~/Documents$ cat -n p1.py 
# -b表示对文件的非空输出行编号
# -n表示对文件的所有行输出行编号
```

``` bash
~/Documents$ cat  p1.py > p2.py
~/Documents$ cat  p2.py
# > 将文件的内容放到另一个文件里，这里我们将p1.py的内容写入到p2.py里面。
```

``` bash
~/Documents$ cat  p1.py p2.py > p3.py
~/Documents$ cat  p3.py
# > 将多个文件的内容打包一起放入另一个文件，这里我们将p1.py的内容和p1.py的内容一同写入到p2.py里面。
# 在显示的时候，两个文件的内容会分开显示
```

``` bash
~/Documents$ cat  p2.py >> p3.py
~/Documents$ cat  p3.py
# >> 将内容添加在一个文件末尾，这里我们将p2.py的内容添加到p3.py的末尾。
```

---

## more

和上面的`cat`命令类似,`more`命令可以用于分屏显示文件内容，每次只显示一页内容。
适合于**查看内容较多**的文本文件。

``` bash
~/Documents$ more  p1.py
# 分屏查看p1.py文件的内容。如果要查看后续的内容，可以按住空格键(显示手册页的下一屏)或者回车键(一次滚动手册页的一行)
# b:回滚一屏  f:前滚一屏  q:退出
```

---

## head

查看文件的前几行(默认查看前10行)

``` bash
~/Documents$ head -n 5 p2.py
# head [-n number] filename
# -n ：后面接数字，代表显示几行的意思
```

---

## tail

查看文件的后几行(默认查看倒数后10行)

``` bash
~/Documents$ tail -n 6 p3.py
# tail [-n number] filename
# -n ：后面接数字，代表显示几行的意思
```

---

## grep

你需要找到一个文件，但是又记不得它的确切位置或路径。`grep`可以帮助你解决这个问题。你可以使用`grep`命令，根据给定的关键字帮助找到文件。

`grep`命令是Linux系统中一种强大的文本搜索工具，它能使用**正则表达式**搜索文本，并把匹配的行打印出来。

常用的两种**正则表达式**查找：

`^a`	行首，搜寻以 a 开头的行
`ke$`	行尾，搜寻以 ke 结束的行

```bash
$ grep [-acinv] [--color=auto] 搜寻字符串 filename
# -a ： 将 binary 文件以 text 文件的方式进行搜寻
# -c ： 计算找到个数
# -i ： 忽略大小写
# -n ： 输出行号
# -v ： 反向选择，亦即显示出 没有搜寻字符串内容 的那一行
# --color=auto ：找到的关键字加颜色显示
```

``` bash
~/Documents$ grep hello p1.python
# 在p1.py文件中搜索"hello"这个单词
```

范例：把含有 the 字符串的行提取出来(注意默认会有 --color=auto 选项，因此以下内容在 Linux 中有颜色显示 the 字符串)

```bash
$ grep -n 'the' regular_express.txt
# 以下为相应的显示结果
# 8:I can't finish the test.
# 12:the symbol '*' is represented as start.
# 15:You are the best is mean you are the no. 1.
# 16:The world Happy is the same with "glad".
# 18:google is the best tools for search keyword
```

**因为 `{ `和 `} `在 `shell` 是有特殊意义的，因此必须要使用转义字符进行转义。**

```bash
$ grep -n 'go\{2,5\}g' regular_express.txt
```

`grep`还有很多更强大的用法，后续会继续更新。当然，具体的用法可以通过`grep --help`进行查看。

---

## echo

`echo`会在终端中显示参数指定的文字,通常会和**重定向**联合使用。

**重定向**`>`和`>>`:
* Linux 允许将命令执行结果**重定向**到一个**文件**
* 将本应显示在**终端上的内容 输出／追加**到**指定文件**中

其中：
* `>`表示输出，会覆盖文件原有的内容。
* `>>`表示追加，会将内容追加到已有文件的末尾。
  
```bash
~/Documents$ echo Hello World > 1.txt
~/Documents$ echo Python >> 1.txt
# 将'Hello World'写入到1.txt中
# 将'Python'追加到1.txt文件的末尾
```
**管道** `|`
* Linux允许将**一个命令的输出**可以**通过管道**做为**另一个命令的输入**
* 可以理解现实生活中的管子，管子的一头塞东西进去，另一头取出来，这里`|`的左右分为两端，左端塞东西（写），右端取东西（读）
  
**常用的管道命令**有：
* `more`: 分屏显示内容
* `grep`: 在命令执行结果的基础上查询指定的文本

```bash
~/Documents$ ls -lha | more
```

---

## apt-get

就不同的发行版而言，这个命令各不相同。在基于Debian的Linux发行版中，想安装、移除和升级任何软件包，我们可以使用高级包装工具(APT)软件包管理器。`apt-get`命令可帮助你安装需要在Linux中运行的软件。这是个功能强大的命令行工具，可以执行安装、升级、甚至移除软件这类任务。

在其他发行版(比如Fedora和Centos)中，有不同的软件包管理器。Fedora过去有`yum`，但现在它有`dnf`。

``` bash
~ sudo apt-get update
```

``` bash
~ sudo dnf update
```

``` bash
~ sudo apt-get install <package name>
# 安装相应的软件或工具
```

---

## poweroff

有时候，你需要直接从终端来进行关机。这个命令就能完成这项任务，别忘了在命令的开头添加`sudo`，因为它需要根权限才能执行`poweroff`。

```bash
~$ sudo poweroff
```

---

## 结束语

好了，基本的`Linux`命令大概就这么多。它会帮助你在这个早期阶段开始使用`Linux`，借助这些基本的`Linux`命令，开始使用`Linux`，并且定个目标：每天学会使用1个至3个命令。后续我也会继续更新~
