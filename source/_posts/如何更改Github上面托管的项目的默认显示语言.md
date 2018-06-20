---
title: 如何更改Github上面托管的项目的默认显示语言
categories: 版本控制之Git
tags: Git
date: 2018-04-22 18:41:32
---

最近将自己一门课的课程大作业，也算是一个小项目demo，放到了GitHub上面，由于项目里面包含许多下载的html文件，导致html代码远远的超过自己写的python代码，于是GitHub默认也就显示是html。

这种对于我这种有强迫症的人来说，简直就是不能忍受的。于是我就各种上网找解决方案。终于，在不懈的努力之后，让我给找到了，在这里分享给大家。<!--more-->

导致这种情况的主要原因是GitHub是使用Linguist来检测你的项目所使用的语言，Linguist具体的作用我个人猜测应该就是：统计你这个项目里面哪一种语言的代码量最多，就把这种语言作为当前这个项目的主语言,也就是默认显示语言。这种做法显然是很不合理的，你比如像python这种支持函数式编程而且崇尚语法简洁优雅的解释型脚本语言，它的代码量远远比不过其他的编译型语言(比如C/C++/Java)。

那么到底如何来解决这个问题呢？解决办法如下：

<font face="微软雅黑" size=3 color=red>使用.gitattributes配置文件</font>

**具体做法是这样的：在你的GitHub代码仓库的根目录界面新建一个.gitattributes配置文件，如下图所示：**

![Github](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/%E5%A6%82%E4%BD%95%E6%9B%B4%E6%94%B9Github%E4%B8%8A%E9%9D%A2%E6%89%98%E7%AE%A1%E7%9A%84%E9%A1%B9%E7%9B%AE%E7%9A%84%E9%BB%98%E8%AE%A4%E6%98%BE%E7%A4%BA%E8%AF%AD%E8%A8%80/Github.png)

然后打开这个文件：把默认的显示语言（也就是统计的代码量最多的语言）全部改成你这个项目本来的语言。例如：

``` bash
*.js linguist-language=Python　　
*.css linguist-language=Python　　　
*.html linguist-language=Python
```

<font face="微软雅黑" size=3 color=red>这几行代码的意思就是说：将以.js , .css , .html 为扩展名的文件都按照Python语言来统计。就是这么简单。这么直接！！！</font>

好了，改完这些之后保存，再回到GitHub主界面就会看到默认的展示语言已经变成python了。主要的步骤就是这样，希望能够帮助到大家。