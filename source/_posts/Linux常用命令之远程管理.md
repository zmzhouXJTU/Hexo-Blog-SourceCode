---
title: Linux常用命令之远程管理.md
date: 2018-10-20 18:25:06
categories: Linux操作系统
tags: Linux
---

之前的一篇文章讲到了Linux操作系统的与目录和文件相关的终端命令，目录和文件相关的命令也是我们用的最多的命令。这篇文章我们来谈谈与**远程管理**有关的终端命令，主要包含以下三块：<!--more-->
* **关机/重启**：
<b> `shutdown`
<br>
* **查看或者配置网卡信息**：
<b> `ifconfig`
<b> `ping`
<br>
* **远程登录和复制文件**：
<b> `ssh`
<b> `scp`

下面我们就来具体来看看这几个命令。

---

## shutdown

`shutdown`命令可以**安全关闭**或者**重新启动系统**。

常见的用法为：

```bash
~/Documents$ shutdown 选项 时间
```
**注意**：
* **不指定选项和参数**，默认表示**1分钟**之后**关闭电脑。**
* 远程维护服务器时，最好不要关闭系统，而应该重新启动系统。

```bash
~/Documents$ shutdown -r
# -r表示重新启动系统
```
**常用命令示例：**

```bash
# 重新启动操作系统，其中 now 表示现在
$ shutdown -r now

# 立刻关机，其中 now 表示现在
$ shutdown now

# 系统在今天的 20:25 会关机
$ shutdown 20:25

# 系统再过十分钟后自动关机
$ shutdown +10

# 取消之前指定的关机计划
$ shutdown -c
```

---

## ifconfig

`ifconfig`可以查看/配置计算机当前的网卡配置信息

```bash
# 查看网卡配置信息
$ ifconfig

# 查看网卡对应的 IP 地址
$ ifconfig | grep inet
```
**提示：**
* 一台计算机中有可能会有一个**物理网卡**和**多个虚拟网卡**，在 Linux中物理网卡的名字通常以`ensXX`表示.
* `127.0.0.1`被称为**本地回环/环回地址**，一般用来测试本机网卡是否正常

---

## ping

`ping`一般用于检测当前计算机到目标计算机之间的网络**是否通畅，数值越大，速度越慢**

```bash
# 检测到目标主机是否连接正常
$ ping IP地址

# 检测本地网卡工作正常
$ ping 127.0.0.1
```
**Notes:**
* `ping`的工作原理与潜水艇的声纳相似，`ping`这个命令就是取自**声纳的声音**
* 网络管理员之间也常将`ping`用作动词——`ping`一下计算机X，看他是否开着
  
**`ping`的原理：
网络上的机器都有**唯一确定的IP地址**，我们给**目标IP地址**发送一个数据包，对方就要返回一个数据包，根据返回的数据包以及时间，我们可以确定目标主机的存在

---

## ssh基础(重点)

在Linux中`SSH`是非常常用的工具，通过`SSH客户端`我们可以连接到运行了`SSH服务器`的远程机器上.

![SSH示意图](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Linux%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E4%B9%8B%E8%BF%9C%E7%A8%8B%E7%AE%A1%E7%90%86/001_SSH%E7%A4%BA%E6%84%8F%E5%9B%BE.png 'SSH示意图')
<br>

* **SSH客户端**是一种使用`Secure Shell(SSH)`协议连接到远程计算机的软件程序
* `SSH`是目前较可靠，**专为远程登录会话和其他网络服务**提供安全性的协议
    * 利用`SSH协议`可以有效**防止远程管理过程中的信息泄露**
    * 通过`SSH协议`可以对所有传输的数据进行加密，也能够防止DNS欺骗和IP欺骗
* `SSH`的另一项优点是传输的数据可以是经过压缩的，所以可以加快传输的速度

1) 域名和端口号

<b>域名:
* 由一串**用点分隔**的名字组成，例如：www.baidu.com
* 是**IP地址**的别名，方便用户记忆

<b>端口号:
* **IP地址**: 通过**IP地址**找到网络上的**计算机**
* **端口号**: 通过**端口号**可以找到**计算机上运行的应用程序**
    * `SSH服务器`的默认端口号是`22`,如果是默认端口号，在连接的时候，可以忽略
* 常见服务器端口号：

| 序号 | 服务 | 端口号 |
|:----:|:----:|:----:|
| 01 | SSH服务器 | 22 |
| 02 | Web服务器 | 80 |
| 03 |HTTPS服务器| 443|
| 04 | FTP服务器 | 21 |

1) SSH客户端的简单使用

```bash
ssh [-p port] user@remote
```
* `user`是在远程机器上的用户名,如果不指定的话默认为当前用户
* `remote`是远程机器的地址，可以是**IP/域名**，或者是**后面会提到的别名**
* `port`是**SSH Server监听的端口**，如果不指定，就默认为`22`
* 使用`exit`退出当前用户的登录

**注意：**
* `SSH`这个终端命令只能在`Linux`或者`UNIX`系统下使用
* 如果在`Windows`系统中，可以安装`PuTTY`或者`XShell`客户端软件即可

---

## scp

`scp`就是`secure copy`，是一个在Linux下用来进行**远程拷贝文件**的命令
它的**地址格式与SSH基本相同，需要注意的是，** 在指定端口时用的是大写的`-P`而不是小写的

![SCP示意图](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Linux%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E4%B9%8B%E8%BF%9C%E7%A8%8B%E7%AE%A1%E7%90%86/002_SCP%E7%A4%BA%E6%84%8F%E5%9B%BE.png 'SCP示意图')

```bash
# 把本地当前目录下的01.py文件复制到远程Home目录下的Desktop/01.py
# 注意：`:` 后面的路径如果不是绝对路径，则以用户的家目录作为参照路径
scp -P port 01.py user@remote:Desktop/01.py

# 把远程Home目录下的Desktop/01.py文件复制到本地当前目录下的01.py
scp -P port user@remote:Desktop/01.py 01.py

# 加上-r选项可以传送文件夹
# 把当前目录下的demo文件夹复制到远程Home目录下的Desktop
scp -r demo user@remote:Desktop

# 把远程Home目录下的Desktop复制到当前目录下的demo文件夹
scp -r user@remote:Desktop demo
```

| 选项 | 含义 |
|:---:|:---:|
|-r|若给出的源文件是目录文件，则 scp 将递归复制该目录下的所有子目录和文件，目标文件必须为一个目录名|
|-P|若远程 SSH 服务器的端口不是 22，需要使用大写字母 -P 选项指定端口|

**注意：**
* `scp`这个终端命令只能在`Linux`或者`UNIX`系统下使用
* 如果在`Windows`系统中，可以安装`PuTTY`, 使用`pscp`命令行工具或者安装 `FileZilla`使用`FTP`进行文件传输
* `FileZilla`在传输文件时，使用的是`FTP服务`而不是`SSH服务`，因此端口号应该设置为 `21`

---

## ssh高级(知道)

* 免密码登陆
* 配置别名

> 注意：有关`SSH`配置信息都保存在用户家目录下的`.ssh`目录下

1) **免密码登陆**：

**步骤：**
* 配置公钥：
    * 执行`ssh-keygen`即可生成`SSH`钥匙，一路回车即可
* 上传公钥到服务器：
    * 执行`ssh-copy-id -p port user@remote`,可以让远程服务器记住我们的公钥
  
**示意图：**

![SSH免密码示意图](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Linux%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E4%B9%8B%E8%BF%9C%E7%A8%8B%E7%AE%A1%E7%90%86/003_SSH%20%E5%85%8D%E5%AF%86%E7%A0%81%E7%A4%BA%E6%84%8F%E5%9B%BE.png 'SSH免密码示意图')

> 非对称加密算法
> * 使用**公钥**加密的数据，需要使用**私钥**解密
> * 使用**私钥**加密的数据，需要使用**公钥**解密

2) **配置别名**：

每次都输入`ssh -p port user@remote`，时间久了会觉得很麻烦，特别是当`user`, `remote`和`port`都得输入，而且还不好记忆

而**配置别名**可以让我们进一步偷懒，譬如用：`ssh mac`来替代上面这么一长串，那么就在 `~/.ssh/config`里面追加以下内容：
```bash
Host mac
    HostName ip地址
    User itheima
    Port 22
```
**保存之后，即可用`ssh mac`实现远程登录了，`scp`同样可以使用**

