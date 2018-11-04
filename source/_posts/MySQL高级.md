---
title: MySQL高级
date: 2018-11-04 13:17:30
categories: 数据库
tags: [SQL, MySQL]
---

上一篇博文我们讲到了`MySQL`与`Python`的交互，也就是如何用`Python`语言来操作`MySQL`数据库，主要也就是进行`CURD`(增删改查)的操作。今天这篇博文是`MySQL`系列的最后一篇博文了，主要讲的是关于`MySQL`的一些高级部分的知识，主要包括：**视图**、**事务**、**索引**以及**账户管理**。好了，话不多说，让我们一起来具体看看！
<!--more-->

---

### 视图

#### 问题

对于复杂的查询，往往是有多个数据表进行关联查询而得到，如果数据库因为需求等原因发生了改变，为了保证查询出来的数据与之前相同，则需要在多个地方进行修改，维护起来非常麻烦。

**解决办法：定义视图**

#### 视图是什么

**通俗的讲，视图就是一条`SELECT`语句执行后返回的结果集**。所以我们在创建视图的时候，主要的工作就落在创建这条`SQL`查询语句上。

**视图是对若干张基本表的引用，一张虚表，查询语句执行的结果**，不存储具体的数据（基本表数据发生了改变，视图也会跟着改变）；

方便操作，特别是查询操作，减少复杂的SQL语句，增强可读性；

#### 定义视图

* 建议以v_开头

```SQL
create view 视图名称 as select语句;
```

#### 查看视图

* 查看表会将所有的视图也列出来

```SQL
show tables;
```

#### 使用视图

* 视图的用途就是查询

```SQL
select * from v_stu_score;
```

#### 删除视图

```SQL
drop view 视图名称;
例：
drop view v_stu_sco;
```

#### 视图demo

![视图样例01](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/%E8%A7%86%E5%9B%BE%E6%A0%B7%E4%BE%8B01.png '视图样例01')

![视图样例02](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/%E8%A7%86%E5%9B%BE%E6%A0%B7%E4%BE%8B02.png '视图样例02')

![视图样例03](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/%E8%A7%86%E5%9B%BE%E6%A0%B7%E4%BE%8B03.png '视图样例03')

![视图样例04](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/%E8%A7%86%E5%9B%BE%E6%A0%B7%E4%BE%8B04.png '视图样例04')

#### 视图的作用

* 提高了重用性，就像一个函数
* 对数据库重构，却不影响程序的运行
* 提高了安全性能，可以对不同的用户
* 让数据更加清晰

---

### 事务

#### 为什么要有事务？

事务广泛的运用于**订单系统、银行系统**等多种场景

例如：

> A用户和B用户是银行的储户，现在A要给B转账500元，那么需要做以下几件事：
> 检查A的账户余额>500元；
> A 账户中扣除500元;
> B 账户中增加500元;

正常的流程走下来，A账户扣了500，B账户加了500，皆大欢喜。

那如果A账户扣了钱之后，系统出故障了呢？A白白损失了500，而B也没有收到本该属于他的500。

以上的案例中，隐藏着一个前提条件：A扣钱和B加钱，要么同时成功，要么同时失败。事务的需求就在于此

所谓**事务,它是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位**。

例如，银行转帐工作：从一个帐号扣款并使另一个帐号增款，这两个操作要么都执行，要么都不执行。所以，应该把他们看成一个事务。事务是数据库维护数据一致性的单位，在每个事务结束时，都能保持数据一致性。

#### 事务四大特性(简称ACID)

* 原子性(Atomicity)
* 一致性(Consistency)
* 隔离性(Isolation)
* 持久性(Durability)

以下内容出自《高性能MySQL》第三版，了解事务的ACID及四种隔离级有助于我们更好的理解事务运作。

下面举一个银行应用是解释事务必要性的一个经典例子。假如一个银行的数据库有两张表：支票表（checking）和储蓄表（savings）。现在要从用户Jane的支票账户转移200美元到她的储蓄账户，那么至少需要三个步骤：

1. 检查支票账户的余额高于或者等于200美元。
2. 从支票账户余额中减去200美元。
3. 在储蓄帐户余额中增加200美元。

**上述三个步骤的操作必须打包在一个事务中，任何一个步骤失败，则必须回滚所有的步骤。**

可以用`START TRANSACTION`语句开始一个事务，然后要么使用`COMMIT`提交将修改的数据持久保存，要么使用`ROLLBACK`撤销所有的修改。事务`SQL`的样本如下：

1. start transaction;
2. select balance from checking where customer_id = 10233276;
3. update checking set balance = balance - 200.00 where customer_id = 10233276;
4. update savings set balance = balance + 200.00 where customer_id = 10233276;
5. commit;

一个很好的事务处理系统，必须具备这些标准特性：

* 原子性(Atomicity)
> 一个事务必须被视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作，这就是事务的原子性

* 一致性(Consistency)
> 数据库总是从一个一致性的状态转换到另一个一致性的状态(在前面的例子中，一致性确保了，即使在执行第三、四条语句之间时系统崩溃，支票账户中也不会损失200美元，因为事务最终没有提交，所以事务中所做的修改也不会保存到数据库中)

* 隔离性(Isolation)
> 通常来说，一个事务所做的修改在最终提交以前，对其他事务是不可见的(在前面的例子中，当执行完第三条语句、第四条语句还未开始时，此时有另外的一个账户汇总程序开始运行，则其看到支票帐户的余额并没有被减去200美元)

* 持久性(Durability)
> 一旦事务提交，则其所做的修改会永久保存到数据库(此时即使系统崩溃，修改的数据也不会丢失)

#### 事务命令

表的引擎类型必须是`innodb`类型才可以使用事务，这是`mysql`表的默认引擎

##### 查看表的创建语句，可以看到`engine=innodb`

```SQL
-- 选择数据库

use jing_dong;

-- 查看goods表

show create table goods;
```

##### 开启事务，命令如下：

* 开启事务后执行修改命令，变更会维护到本地缓存中，而不维护到物理表中

```SQL
begin;

或者

start transaction;
```

##### 提交事务，命令如下:

* 将缓存中的数据变更维护到物理表中

```SQL
commit;
```

##### 回滚事务，命令如下：

* 放弃缓存中变更的数据

```SQL
rollback;
```

##### 注意:

* 修改数据的命令会自动的触发事务，包括`insert、update、delete`
* 而在`SQL`语句中有手动开启事务的原因是：可以进行多次数据的修改，如果成功一起成功，否则一起会滚到之前的数据

---

### 索引

#### 思考

> 在图书馆中是如何找到一本书的?

一般的应用系统对比数据库的读写比例在10:1左右(**即有10次查询操作时有1次写的操作**)，

而且插入操作和更新操作很少出现性能问题，

遇到最多、最容易出问题还是一些复杂的查询操作，所以**查询语句的优化显然是重中之重**

#### 解决办法

当数据库中数据量很大时，查找数据会变得很慢

**优化方案：索引**

#### 索引是什么？

**索引是一种特殊的文件(InnoDB数据表上的索引是表空间的一个组成部分)，它们包含着对数据表里所有记录的引用指针。**

**更通俗的说，数据库索引好比是一本书前面的目录，能加快数据库的查询速度**

#### 索引目的

索引的目的在于**提高查询效率**，可以类比字典，如果要查“mysql”这个单词，我们肯定需要定位到m字母，然后从下往下找到y字母，再找到剩下的`sql`。如果没有索引，那么你可能需要把所有单词看一遍才能找到你想要的，如果我想找到`m`开头的单词呢？或者`ze`开头的单词呢？是不是觉得如果没有索引，这个事情根本无法完成？

#### 索引原理

除了词典，生活中随处可见索引的例子，如火车站的车次表、图书的目录等。它们的原理都是一样的，通过不断的缩小想要获得数据的范围来筛选出最终想要的结果，同时把随机的事件变成顺序的事件，也就是我们总是通过同一种查找方式来锁定数据。

数据库也是一样，但显然要复杂许多，因为不仅面临着等值查询，还有范围查询(>、<、between、in)、模糊查询(like)、并集查询(or)等等。数据库应该选择怎么样的方式来应对所有的问题呢？我们回想字典的例子，能不能把数据分成段，然后分段查询呢？最简单的如果1000条数据，1到100分成第一段，101到200分成第二段，201到300分成第三段……这样查第250条数据，只要找第三段就可以了，一下子去除了90%的无效数据。

![索引](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/%E7%B4%A2%E5%BC%95.jpg '索引')

#### 索引的使用

* 查看索引

```SQL
show index from 表名;
```

* 创建索引
    * 如果指定字段是字符串，需要指定长度，建议长度与定义字段时的长度一致
    * 字段类型如果不是字符串，可以不填写长度部分

```SQL
create index 索引名称 on 表名(字段名称(长度))
```

* 删除索引

```SQL
drop index 索引名称 on 表名;
```

#### 索引demo

##### 创建测试表testindex

```SQL
create table test_index(title varchar(10));
```

##### 使用python程序(`ipython`也可以)通过`pymsql`模块向表中加入十万条数据

```SQL
from pymysql import connect

def main():
    # 创建Connection连接
    conn = connect(host='localhost',port=3306,database='jing_dong',user='root',password='mysql',charset='utf8')
    # 获得Cursor对象
    cursor = conn.cursor()
    # 插入10万次数据
    for i in range(100000):
        cursor.execute("insert into test_index values('ha-%d')" % i)
    # 提交数据
    conn.commit()

if __name__ == "__main__":
    main()
```

##### 查询

* 开启运行时间监测

```SQL
set profiling=1;
```

* 查找第1万条数据`ha-99999`

```SQL
select * from test_index where title='ha-99999';
```

* 查看执行的时间

```SQL
show profiles;
```

* 为表title_index的title列创建索引

```SQL
create index title_index on test_index(title(10));
```

* 执行查询语句

```SQL
select * from test_index where title='ha-99999';
```

* 再次查看执行的时间

```SQL
show profiles;
```

#### 注意

> 要注意的是，建立太多的索引将会影响更新和插入的速度，因为它需要同样更新每个索引文件。对于一个经常需要更新和插入的表格，就没有必要为一个很少使用的`where`子句单独建立索引了，对于比较小的表，排序的开销不会很大，也没有必要建立另外的索引。
> 
>建立索引会占用磁盘空间

---

### 账户管理

* 在生产环境下操作数据库时，绝对不可以使用`root`账户连接，而是创建特定的账户，授予这个账户特定的操作权限，然后连接进行操作，主要的操作就是数据的`CURD`

* `MySQL`账户体系：根据账户所具有的权限的不同，`MySQL`的账户可以分为以下几种:
    * 服务实例级账号：启动了一个`mysqld`，即为一个数据库实例；如果某用户如`root`,拥有服务实例级分配的权限，那么该账号就可以删除所有的数据库、连同这些库中的表
    * 数据库级别账号：对特定数据库执行增删改查的所有操作
    * 数据表级别账号：对特定表执行增删改查等所有操作
    * 字段级别的权限：对某些表的特定字段进行操作
    * 存储程序级别的账号：对存储程序进行增删改查的操作

* 账户的操作主要包括创建账户、删除账户、修改密码、授权权限等

**注意：**
* 进行账户操作时，需要使用root账户登录，这个账户拥有最高的实例级权限
* 通常都使用**数据库级操作权限**

#### 授予权限

需要使用实例级账户登录后操作，以`root`为例

主要操作包括：
* 查看所有用户
* 修改密码
* 删除用户

##### 查看所有用户

* 所有用户及权限信息存储在`mysql`数据库的`user`表中
* 查看`user`表的结构

```SQL
desc user;
```

* 主要字段说明:
    * `Host`表示允许访问的主机 
    * `User`表示用户名
    * `authentication_string`表示密码，为加密后的值

查看所有用户

```SQL
select host,user,authentication_string from user;
```

结果

```SQL
mysql> select host,user,authentication_string from user;

+-----------+------------------+-------------------------------------------+
| host      | user             | authentication_string                     |
+-----------+------------------+-------------------------------------------+
| localhost | root             | *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA |
| localhost | mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| localhost | debian-sys-maint | *EFED9C764966EDB33BB7318E1CBD122C0DFE4827 |
+-----------+------------------+-------------------------------------------+

3 rows in set (0.00 sec)
```

#### 创建账户、授权

* 需要使用实例级账户登录后操作，以`root`为例
* 常用权限主要包括：`create、alter、drop、insert、update、delete、select`
* 如果分配所有权限，可以使用`all privileges`

##### 创建账户&授权

```SQL
grant 权限列表 on 数据库 to '用户名'@'访问主机' identified by '密码';
```

##### 示例1

创建一个`laowang`的账号，密码为`123456`，只能通过`本地`访问, 并且只能对`jing_dong`数据库中的所有表进行`读`操作

###### 使用`root`登录

```SQL
mysql -uroot -p

回车后写密码，然后回车
```

###### 创建账户并授予所有权限

```SQL
grant select on jing_dong.* to 'laowang'@'localhost' identified by '123456';
```

**说明**:
* 可以操作python数据库的所有表，方式为:`jing_dong.*`
* 访问主机通常使用 百分号`%`表示此账户可以使用`任何ip的主机登录`访问此数据库
* 访问主机可以设置成`localhost`或具体的`ip`，表示只允许本机或特定主机访问
* 查看用户有哪些权限

```SQL
show grants for laowang@localhost;
```

###### 退出root的登录

```SQL
quit
```

###### 使用`laowang`账户登录

```SQL
mysql -ulaowang -p

回车后写密码，然后回车
```

* 登录后效果如下图:

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/1.png)

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/2.png)

##### 示例2

创建一个`laoli`的账号，密码为`12345678`，可以`任意电脑`进行链接访问, 并且对`jing_dong`数据库中的所有表拥有`所有权限`

```SQL
grant all privileges on jing_dong.* to "laoli"@"%" identified by "12345678"
```

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/3.png)

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/4.png)

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/5.png)

#### 账户操作

##### 修改权限

```SQL
grant 权限名称 on 数据库 to 账户@主机 with grant option;
```

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/6.png)

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/7.png)

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/8.png)

##### 修改密码

使用`root`登录，修改`mysql`数据库的`user`表
* 使用`password()`函数进行密码加密

```SQL
update user set authentication_string=password('新密码') where user='用户名';
例：
update user set authentication_string=password('123') where user='laowang';
```

* 注意修改完成后需要刷新权限

```SQL
刷新权限：flush privileges
```

##### 远程登录(危险慎用)

如果向在一个`Ubuntu`中使用`msyql`命令远程连接另外一台`mysql`服务器的话，通过以下方式即可完成，但是**此方法仅仅了解就好了，不要在实际生产环境中使用**

修改`/etc/mysql/mysql.conf.d/mysqld.cnf`文件

```bash
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/9.png)

然后重启`msyql`

```bash
service mysql restart
```

在另外一台`Ubuntu`中进行连接测试

![](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E9%AB%98%E7%BA%A7/10.png)

如果依然连不上，可能原因：

1) 网络不通
> 通过`ping xxx.xxx.xx.xxx`可以发现网络是否正常


2) 查看数据库是否配置了`bind_address`参数
> 本地登录数据库查看my.cnf文件和数据库当前参数show variables like 'bind_address';
> 
>如果设置了bind_address=127.0.0.1 那么只能本地登录

3) 查看数据库是否设置了`skip_networking`参数
> 如果设置了该参数，那么只能本地登录mysql数据库

4) 端口指定是否正确

##### 删除账户

* 语法1：使用`root`登录

```SQL
drop user '用户名'@'主机';
例：
drop user 'laowang'@'%';
```

* 语法2：使用`root`登录，删除`mysql`数据库的`user`表中数据

```SQL
delete from user where user='用户名';

例：

delete from user where user='laowang';

-- 操作结束之后需要刷新权限

flush privileges
```

* 推荐使用语法1删除用户, 如果使用语法1删除失败，采用语法2方式

#### 忘记`root`账户密码怎么办?

* 一般也轮不到我们来管理`root`账户,所以别瞎卖白粉的心了
* 万一呢? 到时候再来查<http://blog.csdn.net/lxpbs8851/article/details/10895085>