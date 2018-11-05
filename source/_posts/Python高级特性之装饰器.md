---
title: Python高级特性之装饰器
date: 2018-11-04 18:01:17
categories: Python
tags: Python
---

在代码运行期间动态增加功能的方式，称之为“装饰器”(Decorator)。**装饰器**是程序开发中经常会用到的一个功能，<b>用好了装饰器，开发效率如虎添翼，所以这也是`Python`面试中必问的问题</b>，但是对于好多初次接触这个知识的人来讲，这个功能有点绕，自学时直接绕过去了，然后面试问到了就挂了。因为装饰器是程序开发的基础知识，这个都不会，别跟人家说你会`Python`, 看了下面的文章，保证你学会装饰器。
<!--more-->

---

### 先明白这段代码

```Python

#### 第一波 ####

def foo():
    print('foo')

foo  # 表示是函数
foo()  # 表示执行foo函数

#### 第二波 ####

def foo():
    print('foo')

foo = lambda x: x + 1

foo()  # 执行lambda表达式，而不再是原来的foo函数，因为foo这个名字被重新指向了另外一个匿名函数

```

函数名仅仅是个变量，只不过指向了定义的函数而已，所以才能通过`函数名()`调用，如果`函数名=xxx`被修改了，那么当在执行`函数名()`时，调用的就不是之前的那个函数了。

---

### 需求来了

初创公司有N个业务部门，基础平台部门负责提供底层的功能，如：数据库操作、redis调用、监控API等功能。业务部门使用基础功能时，只需调用基础平台提供的功能即可。如下：

```Python

############### 基础平台提供的功能如下 ###############

def f1():
    print('f1')

def f2():
    print('f2')

def f3():
    print('f3')

def f4():
    print('f4')

############### 业务部门A 调用基础平台提供的功能 ###############

f1()
f2()
f3()
f4()

############### 业务部门B 调用基础平台提供的功能 ###############

f1()
f2()
f3()
f4()

```

目前公司有条不紊的进行着，但是，以前基础平台的开发人员在写代码时候没有关注验证相关的问题，即：基础平台的提供的功能可以被任何人使用。现在需要对基础平台的所有功能进行重构，为平台提供的所有功能添加验证机制，即：**执行功能前，先进行验证。**

<b>老大把工作交给Low B，他是这么做的：</b>

> 跟每个业务部门交涉，每个业务部门自己写代码，调用基础平台的功能之前先验证。诶，这样一来基础平台就不需要做任何修改了。太棒了，有充足的时间泡妹子...

当天Low B 被开除了…

<b>老大把工作交给Low BB，他是这么做的：</b>

```Python

############### 基础平台提供的功能如下 ############### 

def f1():
    # 验证1
    # 验证2
    # 验证3
    print('f1')

def f2():
    # 验证1
    # 验证2
    # 验证3
    print('f2')

def f3():
    # 验证1
    # 验证2
    # 验证3
    print('f3')

def f4():
    # 验证1
    # 验证2
    # 验证3
    print('f4')

############### 业务部门不变 ############### 
### 业务部门A 调用基础平台提供的功能### 

f1()
f2()
f3()
f4()

### 业务部门B 调用基础平台提供的功能 ### 

f1()
f2()
f3()
f4()

```

过了一周 Low BB 被开除了…

<b>老大把工作交给Low BBB，他是这么做的：</b>

> 只对基础平台的代码进行重构，其他业务部门无需做任何修改

```Python

############### 基础平台提供的功能如下 ############### 

def check_login():
    # 验证1
    # 验证2
    # 验证3
    pass


def f1():

    check_login()

    print('f1')

def f2():

    check_login()

    print('f2')

def f3():

    check_login()

    print('f3')

def f4():

    check_login()

    print('f4')

```

老大看了下Low BBB的实现，嘴角漏出了一丝的欣慰的笑，语重心长的跟Low BBB聊了个天：

**老大说：**

写代码要遵循`开放封闭`原则，虽然在这个原则是用的`面向对象开发`，但是也适用于`函数式编程`，简单来说，**它规定已经实现的功能代码不允许被修改，但可以被扩展**，即：
* 封闭：已实现的功能代码块
* 开放：对扩展开发

如果将`开放封闭原则`应用在上述需求中，那么就不允许在函数f1, f2, f3, f4的内部进行修改代码，老板就给了Low BBB一个实现方案：

```Python

def w1(func):
    def inner():
        # 验证1
        # 验证2
        # 验证3
        func()
    return inner

@w1
def f1():
    print('f1')
@w1
def f2():
    print('f2')
@w1
def f3():
    print('f3')
@w1
def f4():
    print('f4')

```

对于上述代码，也是仅仅对基础平台的代码进行修改，就可以实现在其他人调用函数`f1, f2, f3, f4`之前都进行【验证】操作，并且其他业务部门无需做任何操作。

Low BBB心惊胆战的问了下，这段代码的内部执行原理是什么呢？

老大正要生气，突然Low BBB的手机掉到地上，恰巧屏保就是Low BBB的女友照片，老大一看一紧一抖，喜笑颜开，决定和Low BBB交个好朋友。

详细的开始讲解了：

**单独以f1为例：**

```Python

def w1(func):
    def inner():
        # 验证1
        # 验证2
        # 验证3
        func()
    return inner

@w1
def f1():
    print('f1')

```

`python`解释器就会从上到下解释代码，步骤如下：

* def w1(func): ==>将w1函数加载到内存
* @w1

没错，从表面上看解释器仅仅会解释这两句代码，因为函数在没有被调用之前其内部代码不会被执行。

从表面上看解释器着实会执行这两句，但是`@w1`这一句代码里却有大文章，`@函数名`是`python`的一种语法糖。

<b>上例@w1内部会执行一下操作: </b>

**执行w1函数**

> 执行`w1`函数, 并将`@w1`下面的函数作为w1函数的参数，即：`@w1`等价于`w1(f1)`所以，内部就会去执行：
> 
> ```Python
> def inner(): 
>     #验证 1
>     #验证 2
>     #验证 3
>     f1()    # func是参数，此时func等于f1 
> return inner  # 返回的inner，inner代表的是函数，非执行函数 ,其实就是将原来的 f1 函数塞进另外一个函数中
> ```

**w1的返回值**

> 将执行完的`w1`函数返回值 赋值给`@w1`下面的函数的函数名`f1`即将`w1`的返回值再重新赋值给`f1`，即：
> ```Python
> 新f1 = def inner(): 
>             #验证 1
>             #验证 2
>             #验证 3
>             原来f1()
>        return inner
> ```
>所以，以后业务部门想要执行`f1`函数时，就会执行`新f1`函数，在`新f1`函数内部先执行验证，再执行原来的`f1`函数，然后将原来`f1`函数的返回值返回给了业务调用者。

如此一来， 即执行了验证的功能，又执行了原来`f1`函数的内容，并将原`f1`函数返回值 返回给业务调用者

Low BBB你明白了吗？要是没明白的话，我晚上去你家帮你解决吧！！！

---

### 再议装饰器

```Python

# 定义函数：完成包裹数据

def makeBold(fn):
    def wrapped():
        return "<b>" + fn() + "</b>"
    return wrapped

# 定义函数：完成包裹数据

def makeItalic(fn):
    def wrapped():
        return "<i>" + fn() + "</i>"
    return wrapped

@makeBold
def test1():
    return "hello world-1"

@makeItalic
def test2():
    return "hello world-2"

@makeBold
@makeItalic
def test3():
    return "hello world-3"

print(test1())
print(test2())
print(test3())

```

**运行结果:**

```html
<b>hello world-1</b>
<i>hello world-2</i>
<b><i>hello world-3</i></b>
```

---

### 装饰器(decorator)功能

* 引入日志
* 函数执行时间统计
* 执行函数前预备处理
* 执行函数后清理功能
* 权限校验等场景
* 缓存

---

### 装饰器示例

#### 例1:无参数的函数

```Python

from time import ctime, sleep

def timefun(func):
    def wrapped_func():
        print("%s called at %s" % (func.__name__, ctime()))
        func()
    return wrapped_func

@timefun
def foo():
    print("I am foo")

foo()
sleep(2)
foo()

```

上面代码理解装饰器执行行为可理解成:

```Python

foo = timefun(foo)
# foo先作为参数赋值给func后,foo接收指向timefun返回的wrapped_func

foo()
# 调用foo(),即等价调用wrapped_func()
# 内部函数wrapped_func被引用，所以外部函数的func变量(自由变量)并没有释放
# func里保存的是原foo函数对象

```

#### 例2:被装饰的函数有参数

```Python

rom time import ctime, sleep

def timefun(func):
    def wrapped_func(a, b):
        print("%s called at %s" % (func.__name__, ctime()))
        print(a, b)
        func(a, b)
    return wrapped_func

@timefun
def foo(a, b):
    print(a+b)

foo(3,5)
sleep(2)
foo(2,4)

```

#### 例3:被装饰的函数有不定长参数

```Python

from time import ctime, sleep

def timefun(func):
    def wrapped_func(*args, **kwargs):
        print("%s called at %s"%(func.__name__, ctime()))
        func(*args, **kwargs)
    return wrapped_func

@timefun
def foo(a, b, c):
    print(a+b+c)

foo(3,5,7)
sleep(2)
foo(2,4,9)

```

#### 例4:装饰器中的return

```Python

from time import ctime, sleep

def timefun(func):
    def wrapped_func():
        print("%s called at %s" % (func.__name__, ctime()))
        func()
    return wrapped_func

@timefun
def foo():
    print("I am foo")

@timefun
def getInfo():
    return '----hahah---'

foo()
sleep(2)
foo()


print(getInfo())

```

**执行结果:**

```Python

foo called at Fri Nov  4 21:55:35 2016
I am foo
foo called at Fri Nov  4 21:55:37 2016
I am foo
getInfo called at Fri Nov  4 21:55:37 2016
None

```

如果修改装饰器为`return func()`，则运行结果：

```Python

foo called at Fri Nov  4 21:55:57 2016
I am foo
foo called at Fri Nov  4 21:55:59 2016
I am foo
getInfo called at Fri Nov  4 21:55:59 2016
----hahah---

```

**总结：**

* 一般情况下为了让装饰器更通用，可以有`return`

#### 例5:装饰器带参数,在原有装饰器的基础上，设置外部变量

```Python

#decorator2.py

from time import ctime, sleep

def timefun_arg(pre="hello"):
    def timefun(func):
        def wrapped_func():
            print("%s called at %s %s" % (func.__name__, ctime(), pre))
            return func()
        return wrapped_func
    return timefun

# 下面的装饰过程
# 1. 调用timefun_arg("itcast")
# 2. 将步骤1得到的返回值，即time_fun返回， 然后time_fun(foo)
# 3. 将time_fun(foo)的结果返回，即wrapped_func
# 4. 让foo = wrapped_fun，即foo现在指向wrapped_func
@timefun_arg("itcast")
def foo():
    print("I am foo")

@timefun_arg("python")
def too():
    print("I am too")

foo()
sleep(2)
foo()

too()
sleep(2)
too()

```

**可以理解为:**

```Python
foo()==timefun_arg("itcast")(foo)()
```

#### 例6：类装饰器（扩展，非重点）

装饰器函数其实是这样一个接口约束，它必须接受一个`callable`对象作为参数，然后返回一个`callable`对象。在`Python`中一般`callable`对象都是函数，但也有例外。只要某个对象重写了`__call__()`方法，那么这个对象就是`callable`的。

```Python

class Test():
    def __call__(self):
        print('call me!')

t = Test()
t()  # call me

```

**类装饰器demo**

```Python

class Test(object):
    def __init__(self, func):
        print("---初始化---")
        print("func name is %s"%func.__name__)
        self.__func = func
    def __call__(self):
        print("---装饰器中的功能---")
        self.__func()
#说明：

#1. 当用Test来装作装饰器对test函数进行装饰的时候，首先会创建Test的实例对象
#   并且会把test这个函数名当做参数传递到__init__方法中
#   即在__init__方法中的属性__func指向了test指向的函数
#
#2. test指向了用Test创建出来的实例对象
#
#3. 当在使用test()进行调用时，就相当于让这个对象()，因此会调用这个对象的__call__方法
#
#4. 为了能够在__call__方法中调用原来test指向的函数体，所以在__init__方法中就需要一个实例属性来保存这个函数体的引用
#   所以才有了self.__func = func这句代码，从而在调用__call__方法中能够调用到test之前的函数体

@Test
def test():
    print("----test---")
test()
showpy() #如果把这句话注释，重新运行程序，依然会看到"--初始化--"

```

**运行结果如下：**

```Python

---初始化---
func name is test
---装饰器中的功能---
----test---

```
