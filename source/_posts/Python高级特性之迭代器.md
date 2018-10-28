---
title: Python高级特性之迭代器
date: 2018-10-28 17:20:47
categories: Python
tags: Python
---

> 迭代是访问**集合元素**的一种方式。迭代器是一个可以记住遍历的位置的对象。迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。迭代器只能往前不会后退。
<!--more-->

---

## 可迭代对象

我们已经知道可以对`list`、`tuple`、`str`等类型的数据使用`for ... in ...`的循环语法从其中依次拿到数据进行使用，我们把这样的过程称为**遍历**，也叫**迭代。**

但是，是否**所有的数据类型**都可以放到`for ... in ...`的语句中，然后让`for ... in ...`每次从中取出一条数据供我们使用，即供我们迭代吗？

```Python
>>> for i in 100:
...     print(i)
...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not iterable
>>>
# int整型不是iterable，即int整型不是可以迭代的

# 我们自定义一个容器MyList用来存放数据，可以通过add方法向其中添加数据
>>> class MyList(object):
...     def __init__(self):
...             self.container = []
...     def add(self, item):
...             self.container.append(item)
...
>>> mylist = MyList()
>>> mylist.add(1)
>>> mylist.add(2)
>>> mylist.add(3)
>>> for num in mylist:
...     print(num)
...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'MyList' object is not iterable
>>>
# MyList容器的对象也是不能迭代的
```
我们自定义了一个容器类型`MyList`，在将一个存放了多个数据的`MyList`对象放到`for ... in ...`的语句中，发现`for ... in ...`并不能从中依次取出一条数据返回给我们，也就说我们随便封装了一个可以存放多条数据的类型却并不能被迭代使用。

我们把可以通过`for ... in ...`这类语句迭代读取一条数据供我们使用的对象称之为<b>可迭代对象(Iterable)</b>

---

## 如何判断一个对象是否可迭代

可以使用`isinstance()`判断一个对象是否是`Iterable`对象：

```Python
In [50]: from collections import Iterable

In [51]: isinstance([], Iterable)
Out[51]: True

In [52]: isinstance({}, Iterable)
Out[52]: True

In [53]: isinstance('abc', Iterable)
Out[53]: True

In [54]: isinstance(mylist, Iterable)
Out[54]: False

In [55]: isinstance(100, Iterable)
Out[55]: False
```

---

## 可迭代对象的本质

我们分析对可迭代对象进行迭代使用的过程，发现每迭代一次（即在`for ... in ...`中每循环一次）都会返回对象中的下一条数据，一直向后读取数据直到迭代了所有数据后结束。那么，在这个过程中就应该有一个“人”去记录每次访问到了第几条数据，以便每次迭代都可以返回下一条数据。我们把这个能帮助我们进行数据迭代的“人”称为**迭代器(Iterator)**

**可迭代对象的本质就是可以向我们提供一个这样的中间“人”即迭代器帮助我们对其进行迭代遍历使用**

**可迭代对象**通过`__iter__方法`向我们提供一个迭代器，我们在**迭代一个可迭代对象的时候，实际上就是先获取该对象提供的一个迭代器，然后通过这个迭代器来依次获取对象中的每一个数据.**

那么也就是说，一个具备了`__iter__方法`的对象，就是一个可迭代对象

```Python
>>> class MyList(object):
...     def __init__(self):
...             self.container = []
...     def add(self, item):
...             self.container.append(item)
...     def __iter__(self):
...             """返回一个迭代器"""
...             # 我们暂时忽略如何构造一个迭代器对象
...             pass
...
>>> mylist = MyList()
>>> from collections import Iterable
>>> isinstance(mylist, Iterable)
True
>>>
# 这回测试发现添加了__iter__方法的mylist对象已经是一个可迭代对象了
```

---

## iter()函数与next()函数

`list`、`tuple`等都是可迭代对象，我们可以通过`iter()函数`获取这些可迭代对象的迭代器。然后我们可以对获取到的迭代器不断使用`next()函数`来获取下一条数据。`iter()函数`实际上就是调用了可迭代对象的`__iter__方法`。

```Python
>>> li = [11, 22, 33, 44, 55]
>>> li_iter = iter(li)
>>> next(li_iter)
11
>>> next(li_iter)
22
>>> next(li_iter)
33
>>> next(li_iter)
44
>>> next(li_iter)
55
>>> next(li_iter)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>>
```

注意，当我们已经迭代完最后一个数据之后，再次调用`next()函数`会抛出`StopIteration`的异常，来告诉我们所有数据都已迭代完成，不用再执行`next()函数`了

---

## 如何判断一个对象是否是迭代器

可以使用`isinstance()`判断一个对象是否是`Iterator`对象：

```Python
In [56]: from collections import Iterator

In [57]: isinstance([], Iterator)
Out[57]: False

In [58]: isinstance(iter([]), Iterator)
Out[58]: True

In [59]: isinstance(iter("abc"), Iterator)
Out[59]: True
```

---

## 迭代器Iterator

通过上面的分析，我们已经知道，迭代器是用来帮助我们记录每次迭代访问到的位置，当我们对迭代器使用`next()函数`的时候，迭代器会向我们返回它所记录位置的下一个位置的数据。实际上，在使用`next()函数`的时候，调用的就是迭代器对象的`__next__`方法（`Python3`中是对象的`__next__`方法，`Python2`中是对象的`next()`方法）。**所以，我们要想构造一个迭代器，就要实现它的__next__方法**。但这还不够，`python`要求迭代器本身也是可迭代的，所以我们还要为迭代器实现`__iter__`方法，而`__iter__`方法要返回一个迭代器，迭代器自身正是一个迭代器，所以迭代器的`__iter__`方法返回自身即可。

**一个实现了__iter__方法和__next__方法的对象，就是迭代器。**

```Python
class MyList(object):
    """自定义的一个可迭代对象"""
    def __init__(self):
        self.items = []

    def add(self, val):
        self.items.append(val)

    def __iter__(self):
        myiterator = MyIterator(self)
        return myiterator


class MyIterator(object):
    """自定义的供上面可迭代对象使用的一个迭代器"""
    def __init__(self, mylist):
        self.mylist = mylist
        # current用来记录当前访问到的位置
        self.current = 0

    def __next__(self):
        if self.current < len(self.mylist.items):
            item = self.mylist.items[self.current]
            self.current += 1
            return item
        else:
            raise StopIteration

    def __iter__(self):
        return self


if __name__ == '__main__':
    mylist = MyList()
    mylist.add(1)
    mylist.add(2)
    mylist.add(3)
    mylist.add(4)
    mylist.add(5)
    for num in mylist:
        print(num)
```

---

### for ... in ... 的本质

`for item in Iterable`循环的本质就是先通过`iter()`函数获取可迭代对象`Iterable`的迭代器，然后对获取到的迭代器不断调用`next()`方法来获取下一个值并将其赋值给`item`，当遇到`StopIteration`的异常后循环结束。

---

## 迭代器的应用场景

我们发现迭代器最核心的功能就是可以通过`next()`函数的调用来返回下一个数据值。如果每次返回的数据值不是在一个已有的数据集合中读取的，而是通过程序按照一定的规律计算生成的，那么也就意味着可以不用再依赖一个已有的数据集合，也就是说不用再将所有要迭代的数据都一次性缓存下来供后续依次读取，这样可以节省大量的存储（内存）空间。

举个例子，比如，数学中有个著名的斐波拉契数列（Fibonacci），数列中第一个数为0，第二个数为1，其后的每一个数都可由前两个数相加得到：

0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...

现在我们想要通过`for ... in ...`循环来遍历迭代斐波那契数列中的前n个数。那么这个斐波那契数列我们就可以用迭代器来实现，每次迭代都通过数学计算来生成下一个数。

```Python
class FibIterator(object):
    """斐波那契数列迭代器"""
    def __init__(self, n):
        """
        :param n: int, 指明生成数列的前n个数
        """
        self.n = n
        # current用来保存当前生成到数列中的第几个数了
        self.current = 0
        # num1用来保存前前一个数，初始值为数列中的第一个数0
        self.num1 = 0
        # num2用来保存前一个数，初始值为数列中的第二个数1
        self.num2 = 1

    def __next__(self):
        """被next()函数调用来获取下一个数"""
        if self.current < self.n:
            num = self.num1
            self.num1, self.num2 = self.num2, self.num1+self.num2
            self.current += 1
            return num
        else:
            raise StopIteration

    def __iter__(self):
        """迭代器的__iter__返回自身即可"""
        return self


if __name__ == '__main__':
    fib = FibIterator(10)
    for num in fib:
        print(num, end=" ")
```

---

##  并不是只有`for`循环能接收可迭代对象

除了`for`循环能接收可迭代对象，`list`、`tuple`等也能接收。

```Python
li = list(FibIterator(15))
print(li)
tp = tuple(FibIterator(6))
print(tp)
```