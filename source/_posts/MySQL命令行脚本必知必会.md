---
title: MySQL命令行脚本必知必会
date: 2018-10-30 09:39:55
categories: 数据库
tags: [MySQL, SQL]
---

相信学过数据库这门课的人对`SQL`都不会陌生，`SQL`是**结构化查询语言**，是一种用来操作`RDBMS`的数据库语言，当前的主流关系型数据库都支持使用`SQL`语言进行操作, 也就是说可以通过`SQL`操作`Oracle`, `SQL Server`, `MySQL`, `SQLite`等所有主流的关系型的数据库。

`MySQL`是一个关系型数据库管理系统，由瑞典MySQL AB公司开发，后来被Sun公司收购，Sun公司后来又被Oracle公司收购，目前属于Oracle旗下产品。`MySQL`数据库开源、免费、不要钱、使用范围广, 跨平台支持性好, 提供了多种语言调用的`API`是学习数据库开发的首选。

好了讲到这里，就让我们开始**MySQL命令行脚本**的学习吧！
<!--more-->

---

### SQL简介

上面也说了`SQL`是一门**结构化查询语言**，可以用来操作`MySQL`等主流关系型数据库。那么`SQL`语句可以分为哪几类呢？让我们一起来看看。
* SQL语句主要分为:
    * **DQL：数据查询语言，用于对数据进行查询，如select**
    * **DML：数据操作语言，对数据进行增加、修改、删除，如insert、udpate、delete**
    * TPL：事务处理语言，对事务进行处理，包括begin transaction、commit、rollback
    * DCL：数据控制语言，进行授权与权限回收，如grant、revoke
    * **DDL：数据定义语言，进行数据库、表的管理等，如create、drop**
    * CCL：指针控制语言，通过控制指针完成表的操作，如declare cursor

* 对于Web开发的程序员来讲，**重点是数据的CURD(增删改查)，必须熟练编写DQL、DML，能够编写DDL完成数据库、表的操作**, 其它类型语句如TPL、DCL、CCL了解即可。
* `SQL`是一门特殊的语言,专门用来操作关系数据库
* **不区分大小写**

---

### 命令行连接

这里以`Linux`的发行版`Ubuntu 16.04`操作系统为例演示命令行脚本，其他操作系统(如Windows)类似，都是大同小异的操作。

* 打开终端，运行命令：

```bash
mysql -uroot -p
# 回车后输入密码，当前设置的密码为mysql
# 如果不想输入密码，可以直接将密码跟在该命令后面，即 mysql -uroot -pmysql
```

**Note**：`Windows`系统安装完`MySQL`数据库后，直接在开始菜单里面找到`MySQL 8.0 Command Line Client - Unicode`程序, 点击打开，直接输入密码即可登录，无需再输入`mysql -uroot -p`命令

* 连接成功后如下图：

![Ubuntu连接MySQL数据库](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E5%91%BD%E4%BB%A4%E8%A1%8C%E8%84%9A%E6%9C%AC%E5%BF%85%E7%9F%A5%E5%BF%85%E4%BC%9A/Ubuntu%E8%BF%9E%E6%8E%A5MySQL%E6%95%B0%E6%8D%AE%E5%BA%93.png 'Ubuntu连接MySQL数据库')

![Windows连接MySQL数据库](https://myblogs-photos-1256941622.cos.ap-chengdu.myqcloud.com/MySQL%E5%91%BD%E4%BB%A4%E8%A1%8C%E8%84%9A%E6%9C%AC%E5%BF%85%E7%9F%A5%E5%BF%85%E4%BC%9A/Windows%E8%BF%9E%E6%8E%A5MySQL%E6%95%B0%E6%8D%AE%E5%BA%93.png 'Windows连接MySQL数据库')


* 退出登录：

```bash
quit 和 exit
或
ctrl+d
```

* 登陆成功后，输入以下命令查看效果：

```bash
查看版本：select version();
显示当前时间：select now();
```

---

### 数据库操作

* 查看所有数据库

```SQL
show databases;
```

* 使用数据库

```SQL
use 数据库名;
```

* 查看当前使用的数据库

```SQL
select database();
```

* 创建数据库

```SQL
create database 数据库名 charset=utf8;
例：
create database python charset=utf8;
```

* 查看数据库的创建语句

```SQL
show create database 数据库名;
例：
show create database Python;
```

* 删除数据库

```SQL
drop database 数据库名;
例：
drop database python;
```

---

### 数据表操作

* 查看当前数据库中所有表

```SQL
show tables;
```

* 查看表结构

```SQL
desc 表名;
```

* 创建表
    * `auto_increment`表示自动增长

```SQL
CREATE TABLE table_name(
    column1 datatype contrai,
    column2 datatype,
    column3 datatype,
    .....
    columnN datatype,
    PRIMARY KEY(one or more columns)
);
```

例：创建班级表

```SQL
create table classes(
    id int unsigned auto_increment primary key not null,
    name varchar(10)
);
```

创建学生表

```SQL
create table students(
    id int unsigned primary key auto_increment not null,
    name varchar(20) default '',
    age tinyint unsigned default 0,
    height decimal(5,2),
    gender enum('男','女','人妖','保密'),
    cls_id int unsigned default 0
)
```

* 修改表-添加字段

```SQL
alter table 表名 add 列名 类型;
例：
alter table students add birthday datetime;
```

* 修改表-修改字段：重命名版

```SQL
alter table 表名 change 原名 新名 类型及约束;
例：
alter table students change birthday birth datetime not null;
```

* 修改表-修改字段：不重命名版

```SQL
alter table 表名 modify 列名 类型及约束;
例：
alter table students modify birth date not null;
```

* 修改表-删除字段

```SQL
alter table 表名 drop 列名;
例：
alter table students drop birthday;
```

* 删除表

```SQL
drop table 表名;
例：
drop table students;
```

* 查看表的创建语句

```SQL
show create table 表名;
例：
show create table classes;
```

### 数据的增删改查(CURD)

* **CURD**的解释: 代表创建(Create)、更新(Update)、读取(Retrieve)和删除(Delete)
  
#### 查询

* 查询所有列

```SQL
select * from 表名;
例：
select * from classes;
```

* 查询指定列
    * 可以使用`as`为列或表指定别名

```SQL
select 列1,列2,... from 表名;
例：
select id,name from classes;
```

#### 增加

> 格式：INSERT [INTO] tb_name [(col_name,...)] {VALUES | VALUE} ({expr | DEFAULT},...),(...),...

* 说明：主键列是自动增长，但是在全列插入时需要占位，通常使用`0`或者`default`或者`null`来占位, 插入成功后以实际数据为准
* 全列插入：值的顺序与表中字段的顺序对应

```SQL
insert into 表名 values(...)
例：
insert into students values(0,’郭靖‘,1,'蒙古','2016-1-2');
```

* 部分列插入：值的顺序与给出的列顺序对应

```SQL
insert into 表名(列1,...) values(值1,...)
例：
insert into students(name,hometown,birthday) values('黄蓉','桃花岛','2016-3-2');
```

* 上面的语句一次可以向表中插入一行数据，还可以一次性插入多行数据，这样可以减少与数据库的通信
* 全列多行插入:值的顺序与给出的列顺序对应

```SQL
insert into 表名 values(...),(...)...;
例：
insert into classes values(0,'python1'),(0,'python2');
```

* 部分列多行插入:值的顺序与给出的列顺序对应

```SQL
insert into 表名(列1,...) values(值1,...),(值1,...)...;
例：
insert into students(name) values('杨康'),('杨过'),('小龙女');
```

#### 修改

> 格式: UPDATE tbname SET col1={expr1|DEFAULT} [,col2={expr2|default}]...[where 条件判断]

```SQL
update 表名 set 列1=值1,列2=值2... where 条件
例：
update students set gender=0,hometown='北京' where id=5;
```

#### 删除

> DELETE FROM tbname [where 条件判断]

```SQL
delete from 表名 where 条件
例：
delete from students where id=5;
```

* 逻辑删除，本质就是修改操作

```SQL
update students set isdelete=1 where id=1;
```

---

### 备份

* 运行`mysqldump`命令

```SQL
mysqldump –uroot –p 数据库名 > python.sql;

# 按提示输入mysql的密码
```

---

### 恢复

* 连接`mysql`, 创建新的数据库，
* 退出连接，执行如下命令：

```SQL
mysql -uroot –p 新数据库名 < python.sql

# 根据提示输入mysql密码
```