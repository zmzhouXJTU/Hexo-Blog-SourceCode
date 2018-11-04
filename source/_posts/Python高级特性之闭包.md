---
title: Python高级特性之闭包
date: 2018-11-04 17:35:59
categories: Python
tags: Python
---

**闭包(closure)**是**函数式编程**的重要的语法结构。**函数式编程是一种编程范式(而面向过程编程和面向对象编程也都是编程范式)**。在面向过程编程中，我们见到过`函数(function)`；在面向对象编程中，我们见过`对象(object)`。函数和对象的根本目的是以某种逻辑方式组织代码，并提高代码的可重复使用性(reusability)。**闭包也是一种组织代码的结构，它同样提高了代码的可重复使用性。**
<!--more-->

---

不同的语言实现闭包的方式不同。`Python`以函数对象为基础，为闭包这一语法结构提供支持的(我们在特殊方法与多范式中，已经多次看到`Python`使用对象来实现一些特殊的语法)。`Python`一切皆对象，函数这一语法结构也是一个对象。在函数对象中，我们像使用一个普通对象一样使用函数对象，比如更改函数对象的名字，或者将函数对象作为参数进行传递。

### 函数引用

```Python
def test1():
    print("--- in test1 func----")

# 调用函数
test1()

# 引用函数
ret = test1

print(id(ret))
print(id(test1))

#通过引用调用函数
ret()
```

**运行结果：**

```Python
--- in test1 func----
140212571149040
140212571149040
--- in test1 func----
```

### 什么是闭包？

```Python
# 定义一个函数
def test(number):

    # 在函数内部再定义一个函数，并且这个函数用到了外边函数的变量，那么将这个函数以及用到的一些变量称之为闭包
    def test_in(number_in):
        print("in test_in 函数, number_in is %d" % number_in)
        return number+number_in
    # 其实这里返回的就是闭包的结果
    return test_in


# 给test函数赋值，这个20就是给参数number
ret = test(20)

# 注意这里的100其实给参数number_in
print(ret(100))

#注 意这里的200其实给参数number_in
print(ret(200))
```

**运行结果：**

```Python

in test_in 函数, number_in is 100
120

in test_in 函数, number_in is 200
220

```

### 看一个闭包的实际例子

```Python

def line_conf(a, b):
    def line(x):
        return a*x + b
    return line

line1 = line_conf(1, 1)
line2 = line_conf(4, 5)
print(line1(5))
print(line2(5))

```

这个例子中，函数`line`与变量`a,b`构成闭包。在创建闭包的时候，我们通过`line_conf`的参数`a,b`说明了这两个变量的取值，这样，我们就确定了函数的最终形式`(y = x + 1和y = 4x + 5)`。我们只需要变换参数`a,b`，就可以获得不同的直线表达函数。由此，我们可以看到，**闭包也具有提高代码可复用性的作用**。

如果没有闭包，我们需要每次创建直线函数的时候同时说明a,b,x。这样，我们就需要更多的参数传递，也减少了代码的可移植性.

**注意点:**

由于闭包引用了外部函数的局部变量，则外部函数的局部变量没有及时释放，消耗内存

### 修改外部函数中的变量

#### `python3`的方法

```Python

def counter(start=0):
    def incr():
        nonlocal start
        start += 1
        return start
    return incr

c1 = counter(5)
print(c1())
print(c1())

c2 = counter(50)
print(c2())
print(c2())

print(c1())
print(c1())

print(c2())
print(c2())

```

#### `python2`的方法

```Python

def counter(start=0):
    count=[start]
    def incr():
        count[0] += 1
        return count[0]
    return incr

c1 = closeure.counter(5)
print(c1())  # 6
print(c1())  # 7
c2 = closeure.counter(100)
print(c2())  # 101
print(c2())  # 102

```