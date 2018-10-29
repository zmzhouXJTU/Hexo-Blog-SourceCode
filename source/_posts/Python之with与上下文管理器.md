---
title: Python之with与上下文管理器
date: 2018-10-29 15:55:37
categories: Python
tags: Python
---

如果你有阅读源码的习惯，可能会看到一些优秀的代码经常出现带有“with”关键字的语句，它通常用在什么场景呢？

对于系统资源如文件、数据库连接、socket而言，**应用程序打开这些资源并执行完业务逻辑之后，必须做的一件事就是要关闭（断开）该资源**。

比如`Python`程序打开一个文件，往文件中写内容，写完之后，就要关闭该文件，否则会出现什么情况呢？极端情况下会出现`"Too many open files"`的错误，因为系统允许你打开的最大文件数量是有限的。
<!--more-->

同样，对于数据库，如果连接数过多而没有及时关闭的话，就可能会出现`"Can not connect to MySQL server Too many connections"`，因为数据库连接是一种非常昂贵的资源，不可能无限制的被创建。

下面就让我们来看看如何正确关闭一个文件：

---

### 正确关闭一个文件

#### 普通版

```Python
def m1():
    f = open("output.txt", "w")
    f.write("python之禅")
    f.close()
```

这样写有一个潜在的问题，如果在调用`write`的过程中，出现了异常进而导致后续代码无法继续执行，`close`方法无法被正常调用，因此资源就会一直被该程序占用者释放。那么该如何改进代码呢？

#### 进阶版

```Python
def m2():
    f = open("output.txt", "w")
    try:
        f.write("python之禅")
    except IOError:
        print("oops error")
    finally:
        f.close()
```

改良版本的程序是对可能发生异常的代码处进行`try`捕获，使用`try/finally`语句，该语句表示如果在`try`代码块中程序出现了异常，后续代码就不再执行，而直接跳转到`except`代码块。而无论如何，`finally`块的代码最终都会被执行。因此，只要把`close`放在`finally`代码中，文件就一定会关闭

#### 高级版

```Python
def m3():
    with open("output.txt", "r") as f:
        f.write("Python之禅")
```

一种更加简洁、优雅的方式就是用`with`关键字。`open`方法的返回值赋值给变量 `f`，当离开`with`代码块的时候，系统会自动调用`f.close()`方法，`with`的作用和使用`try/finally`语句是一样的。那么它的实现原理是什么？在讲`with`的原理前要涉及到另外一个概念，就是**上下文管理器(Context Manager)**

---

### 什么是上下文(context)

> 上下文在不同的地方表示不同的含义，要感性理解。`context`其实说白了，和文章的上下文是一个意思，在通俗一点，我觉得叫环境更好。....

> 林冲大叫一声“啊也！”....

> 问:这句话林冲的“啊也”表达了林冲怎样的心里？

> 答:啊你妈个头啊！

看，一篇文章，给你摘录一段，没前没后，你读不懂，因为有语境，就是语言环境存在，一段话说了什么，要通过上下文(文章的上下文)来推断。

app点击一个按钮进入一个新的界面，也要保存你是在哪个屏幕跳过来的等等信息，以便你点击返回的时候能正确跳回，如果不存肯定就无法正确跳回了。

看这些都是上下文的典型例子，理解成环境就可以，(而且上下文虽然叫上下文，但是程序里面一般都只有上文而已，只是叫的好听叫上下文。。进程中断在操作系统中是有上有下的，不过不这个高深的问题就不要深究了。。。)

---

### 上下文管理器

任何实现了`__enter__()`和`__exit__()`方法的对象都可称之为**上下文管理器**，上下文管理器对象可以使用`with`关键字。显然，文件（file）对象也实现了上下文管理器。

那么文件对象是如何实现这两个方法的呢？我们可以模拟实现一个自己的文件类，让该类实现`__enter__()`和`__exit__()`方法

```Python
class File():

    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode

    def __enter__(self):
        print("entering")
        self.f = open(self.filename, self.mode)
        return self.f

    def __exit__(self, *args):
        print("will exit")
        self.f.close()
```

`__enter__()`方法返回资源对象，这里就是你将要打开的那个文件对象，`__exit__()`方法处理一些清除工作。

因为`File`类实现了上下文管理器，现在就可以使用`with`语句了

```Python
with File('out.txt', 'w') as f:
    print("writing")
    f.write('hello, python')
```

这样，你就无需显示地调用`close`方法了，由系统自动去调用，哪怕中间遇到异常`close`方法也会被调用

---

### 实现上下文管理器的另外方式

`Python`还提供了一个`contextmanager`的装饰器，更进一步简化了上下文管理器的实现方式。通过`yield`将函数分割成两部分，`yield`之前的语句在 `__enter__ `方法中执行，`yield`之后的语句在`__exit__`方法中执行。紧跟在`yield`后面的值是函数的返回值

```Python
from contextlib import contextmanager

@contextmanager
def my_open(path, mode):
    f = open(path, mode)
    yield f
    f.close()
```

调用

```Python
with my_open('out.txt', 'w') as f:
    f.write("hello , the simplest context manager")
```

---

### 总结

`Python`提供了`with`语法用于简化资源操作的后续清除操作，是`try/finally` 的替代方法，实现原理建立在**上下文管理器**之上。此外，`Python`还提供了一个`contextmanager`装饰器，更进一步简化上下管理器的实现方式。

