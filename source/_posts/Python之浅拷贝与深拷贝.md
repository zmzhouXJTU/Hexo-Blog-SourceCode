---
title: Python之浅拷贝与深拷贝
date: 2018-10-29 11:36:04
categories: Python
tags: Python
---

>在Python中，对象的赋值，拷贝(深/浅拷贝)之间是有差异的，如果使用的时候不注意，就可能产生意外的结果。下面就让我们一起来看看它们之间具体存在的差异吧！
<!--more-->

### 浅拷贝

* **浅拷贝**是对于一个对象的顶层拷贝

**通俗的理解是：拷贝了引用，并没有拷贝内容**

![浅拷贝示意图1](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E6%B5%85%E6%8B%B7%E8%B4%9D%E7%A4%BA%E6%84%8F%E5%9B%BE1.png '浅拷贝示意图1')

![浅拷贝示意图2](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E6%B5%85%E6%8B%B7%E8%B4%9D%E7%A4%BA%E6%84%8F%E5%9B%BE2.png '浅拷贝示意图2')

---

### 深拷贝

* **深拷贝**是对于一个对象所有层次的拷贝(递归)

![深拷贝示意图1](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%A4%BA%E6%84%8F%E5%9B%BE1.png '深拷贝示意图1')

**进一步理解深拷贝**

![深拷贝示意图2](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%A4%BA%E6%84%8F%E5%9B%BE2.png '深拷贝示意图2')

![深拷贝示意图3](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%A4%BA%E6%84%8F%E5%9B%BE3.png '深拷贝示意图3')

---

### 拷贝的其他方式

* **分片表达式**可以赋值一个序列

![分片表达式](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E5%88%86%E7%89%87%E8%A1%A8%E8%BE%BE%E5%BC%8F.png '分片表达式')

* 字典的`copy`方法可以拷贝一个字典

![字典的copy方法](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E5%AD%97%E5%85%B8copy%E6%96%B9%E6%B3%95.png '字典的copy方法')

---

### 注意点

#### 浅拷贝对不可变类型和可变类型的copy不同

* `copy.copy`对于**可变类型，会进行浅拷贝***
* `copy.copy`对于**不可变类型，不会拷贝，仅仅是指向**

```Python
In [88]: a = [11,22,33]
In [89]: b = copy.copy(a)
In [90]: id(a)
Out[90]: 59275144
In [91]: id(b)
Out[91]: 59525600
In [92]: a.append(44)
In [93]: a
Out[93]: [11, 22, 33, 44]
In [94]: b
Out[94]: [11, 22, 33]


In [95]: a = (11,22,33)
In [96]: b = copy.copy(a)
In [97]: id(a)
Out[97]: 58890680
In [98]: id(b)
Out[98]: 58890680
```

![浅拷贝与深拷贝的差异1](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D%E7%9A%84%E5%B7%AE%E5%BC%821.png '浅拷贝与深拷贝的差异1')


#### copy.copy和copy.deepcopy的区别

> copy.copy()

![copy.copy()_01](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/copy.copy()_01.png 'copy.copy()_01')

![copy.copy()_02](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/copy.copy()_02.png 'copy.copy()_02')


> copy.deepcopy()

![copy.deepcopy()_01](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/copy.deepcopy()_01.png 'copy.deepcopy()_01')

![copy.deepcopy()_02](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/copy.deepcopy()_02.png 'copy.deepcopy()_02')

![copy.deepcopy()_03](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/Python%E4%B9%8B%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D/copy.deepcopy()_03.png 'copy.deepcopy()_03')