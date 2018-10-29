---
title: Python之魔法属性
date: 2018-10-29 15:21:58
categories: Python
tags: Python
---

无论是人或事物往往都有不按套路出牌的情况，`Python`的类属性也是如此，存在着一些具有特殊含义的属性。下面就要我们一起来看一下吧！
<!--more-->

### `__doc__`

* 表示类的**描述信息**

```Python
class Foo:
    """ 描述类信息，这是用于看片的神奇 """
    def func(self):
        pass

print(Foo.__doc__)
#输出：类的描述信息
```

---

### `__module__`和`__class__`

* \__module\__ 表示**当前操作的对象在那个模块**
* \__class\__ 表示**当前操作的对象的类是什么**

> test.py

```Python
# -*- coding:utf-8 -*-

class Person(object):
    def __init__(self):
        self.name = 'laowang'
```

> main.py

```Python
from test import Person

obj = Person()
print(obj.__module__)  # 输出 test 即：输出模块
print(obj.__class__)  # 输出 test.Person 即：输出类
```

---

### `__init__`

* 初始化方法，通过**类创建对象时，自动触发执行**

```Python
class Person:
    def __init__(self, name):
        self.name = name
        self.age = 18


obj = Person('laowang')  # 自动执行类中的 __init__ 方法
```

---

### `__del__`

* 当对象在内存中被释放时，自动触发执行

注：此方法一般无须定义，因为`Python`是一门高级语言，程序员在使用时无需关心内存的分配和释放，因为此工作都是交给`Python`解释器来执行，所以，`__del__`的调用是由解释器在进行垃圾回收时自动触发执行的。

```Python
class Foo:
    def __del__(self):
        pass
```

---

### `__call__`

* 对象后面加括号，触发执行

注：`__init__`方法的执行是由创建对象触发的，即：<b>对象 = 类名()</b> ；而对于`__call__`方法的执行是由对象后加括号触发的，即：**对象()或者类()()**

```Python
class Foo:
    def __init__(self):
        pass

    def __call__(self, *args, **kwargs):
        print('__call__')


obj = Foo()  # 执行 __init__
obj()  # 执行 __call__
```

---

### `__dict__`

* 类或对象中的所有属性

类的实例属性属于对象；类中的类属性和方法等属于类，即：

```Python
class Province(object):
    country = 'China'

    def __init__(self, name, count):
        self.name = name
        self.count = count

    def func(self, *args, **kwargs):
        print('func')

# 获取类的属性，即：类属性、方法、
print(Province.__dict__)
# 输出：{'__dict__': <attribute '__dict__' of 'Province' objects>, '__module__': '__main__', 'country': 'China', '__doc__': None, '__weakref__': <attribute '__weakref__' of 'Province' objects>, 'func': <function Province.func at 0x101897950>, '__init__': <function Province.__init__ at 0x1018978c8>}

obj1 = Province('山东', 10000)
print(obj1.__dict__)
# 获取 对象obj1 的属性
# 输出：{'count': 10000, 'name': '山东'}

obj2 = Province('山西', 20000)
print(obj2.__dict__)
# 获取 对象obj1 的属性
# 输出：{'count': 20000, 'name': '山西'}
```

---

### `__str__`

* 如果一个类中定义了`__str__`方法，那么在打印对象时，默认输出该方法的返回值

```Python
class Foo:
    def __str__(self):
        return 'laowang'


obj = Foo()
print(obj)
# 输出：laowang
```

---

### `__getitem__`、`__setitem__`、`__delitem__`

* 用于**索引**操作，如字典。以上分别表示获取、设置、删除数据:

```Python
# -*- coding:utf-8 -*-

class Foo(object):

    def __getitem__(self, key):
        print('__getitem__', key)

    def __setitem__(self, key, value):
        print('__setitem__', key, value)

    def __delitem__(self, key):
        print('__delitem__', key)


obj = Foo()

result = obj['k1']      # 自动触发执行 __getitem__
obj['k2'] = 'laowang'   # 自动触发执行 __setitem__
del obj['k1']           # 自动触发执行 __delitem__
```

---

### `__getslice__`、`__setslice__`、`__delslice__`

* 该三个方法用于**切片**操作，如：列表

```Python
# -*- coding:utf-8 -*-

class Foo(object):

    def __getslice__(self, i, j):
        print('__getslice__', i, j)

    def __setslice__(self, i, j, sequence):
        print('__setslice__', i, j)

    def __delslice__(self, i, j):
        print('__delslice__', i, j)

obj = Foo()

obj[-1:1]                   # 自动触发执行 __getslice__
obj[0:1] = [11,22,33,44]    # 自动触发执行 __setslice__
del obj[0:2]                # 自动触发执行 __delslice__
```