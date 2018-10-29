---
title: Python之静态方法与类方法
date: 2018-10-29 14:43:50
categories: Python
tags: [Python, OOP]
---

我们都知道`Python`语言是一门动态语言，根据类创建的实例可以任意绑定属性。给实例绑定属性的方法是通过实例变量，或者通过`self`变量。但是，如果是类本身需要绑定一个属性呢？可以直接在`class`中定义属性，这种属性是**类属性**，归对应的类所有。今天我们就来看一下`Python`中的**类属性、实例属性以及实例方法、静态方法和类方法**。
<!--more-->

---

### 类属性、实例属性

类属性、实例属性在定义和使用中有所区别，而**最本质**的区别是内存中保存的位置不同

* 实例属性属于对象
* 类属性属于类

```Python
class Province(object):
    # 类属性
    country = '中国'

    def __init__(self, name):
        # 实例属性
        self.name = name


# 创建一个实例对象
obj = Province('山东省')
# 直接访问实例属性
print(obj.name)
# 直接访问类属性
Province.country
```

由上述代码可以看出【实例属性需要通过对象来访问】【类属性通过类访问】，在使用上可以看出实例属性和类属性的归属是不同的.

其在内存中的存储方式类似如下图：

![类属性与实例属性](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E4%B8%8E%E7%B1%BB%E6%96%B9%E6%B3%95/%E7%B1%BB%E5%B1%9E%E6%80%A7%E4%B8%8E%E5%AE%9E%E4%BE%8B%E5%B1%9E%E6%80%A7.png '类属性与实例属性')

由上图可以看出：
* 类属性在内存中只保存一份
* 实例属性在每个对象中都要保存一份

<b>应用场景：</b>
* 通过类创建实例对象时，如果每个对象需要具有相同名字的属性，那么就使用类属性，用一份既可

---

### 实例方法、静态方法和类方法

方法包括：实例方法、静态方法和类方法，**三种方法在内存中都归属于类**，**区别在于调用方式不同**

* 实例方法：由对象调用；至少一个`self`参数；执行实例方法时，自动将调用该方法的对象赋值给`self`；
* 类方法：由类调用； 至少一个`cls`参数；执行类方法时，自动将调用该方法的类赋值给`cls`；
* 静态方法：由类调用；无默认参数

```Python
class Foo(object):
    def __init__(self, name):
        self.name = name

    def ord_func(self):
        """ 定义实例方法，至少有一个self参数 """
        # print(self.name)
        print('实例方法')

    @classmethod
    def class_func(cls):
        """ 定义类方法，至少有一个cls参数 """
        print('类方法')

    @staticmethod
    def static_func():
        """ 定义静态方法 ，无默认参数"""
        print('静态方法')



f = Foo("中国")
# 调用实例方法
f.ord_func()

# 调用类方法
Foo.class_func()

# 调用静态方法
Foo.static_func()
```

![实例方法-类方法-静态方法](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95%E4%B8%8E%E7%B1%BB%E6%96%B9%E6%B3%95/%E5%AE%9E%E4%BE%8B%E6%96%B9%E6%B3%95-%E7%B1%BB%E6%96%B9%E6%B3%95-%E9%9D%99%E6%80%81%E6%96%B9%E6%B3%95.png '实例方法-类方法-静态方法')

**对比：**
* 相同点：对于所有的方法而言，均属于类，所以 在内存中也只保存一份
* 不同点：方法调用者不同、调用方法时自动传入的参数不同