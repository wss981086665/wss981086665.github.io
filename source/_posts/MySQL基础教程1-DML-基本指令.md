---
layout: post
title: MySQL基础教程1-DML(基本指令)
date: 2019-08-07 20:49:28
categories:
- MySQL
tags:
- MySQL
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Mysql并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

# 基本指令操作
## 一、Sql分类
- **数据库查询语言(DQL-Data Query Language)**
                代表关键字: select
- **数据操纵语言(DML-Data Manipulation Language)**
                代表关键字: insert，delete，update
- **数据库定义语言(DDL-Data Definition Language)**
                代表关键字: creat，drop，alter
- 事务控制语言(TCL-Transactional Control Language)
                代表关键字: commit，rollback
- 数据控制语言(DCL-Data Control Language)
                代表关键字: grant，revoke
> 注意: 以下给出的代码块均为示例代码块

## 二、MySQL指令操作
### 1、基本操作
#### (1).创建数据库
``` sql
create database myDatabase;
```
#### (2).查看所有数据库
``` sql
show databases;
```
#### (3).使用数据库
``` sql
use myDatabase;
```
![使用myDatabase数据库.png](https://upload-images.jianshu.io/upload_images/13687958-a6a0058772dcfe40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### (4).在数据库中创建表
``` sql
create table user(
    id int NOT NULL AUTO_INCREMENT,
    name varchar(50) NOT NULL,
    age int NOT NULL,
    PRIMARY KEY(id)
    )ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
#### (5).查看数据库中的所有表
``` sql
show tables;
```
![myDatabase中的表.png](https://upload-images.jianshu.io/upload_images/13687958-30df4c2876d4b433.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### (6).查看表结构
``` sql
desc user;              
```
![user表结构.png](https://upload-images.jianshu.io/upload_images/13687958-7ec33ac9ef915c8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### (7).设置字符编码
``` sql
set names gbk;
```
#### (8).为已创建的表添加字段(列)
```
alter table user add gender int unsigned;
```
### 2、insert操作
``` sql
insert into user (id,name,age) values (1,"wss",18);
```
如果id是自动递增的，也可以这样写:
``` sql
insert into user (name,age) values ("wss",18);
```
如果age设置了默认值并且不需要填写时，也可以这样写:
``` sql
insert into user (name) values ("wss");
```
> 注意：数与值严格对应。
> 数字不必加引号，字符串必须加引号

**一次添加多个数据:**
``` sql
insert into user values 
     (1,'wss',18),
     (2,'xqz',20),
     (3,'xg',19);
```
### 3、update操作
``` sql
update user set age = 10 where name='xqz';
```
![更新'xqz'的年龄](https://upload-images.jianshu.io/upload_images/13687958-a6b0e0a610e28549.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![update图解](https://upload-images.jianshu.io/upload_images/13687958-ffc8d6fa7b2e0606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 如果不加where条件，就是修改表中所有的数据，这是非常危险的做法。
### 4、delete操作
``` sql
delete from user where id=3;
```
清除全部数据:(**`危险的做法`**)
``` sql
delete from user;
```
### 5、select操作
![select语法](https://upload-images.jianshu.io/upload_images/13687958-258d93d88430ace6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
``` sql
select 字段 from user where 条件;
```
**例:**
``` sql
select * from user where id=2;
select * from user where id>=2;
select id,name from user where id >=2;
```
> **`注意`**： 真正开发环境中，要尽量避免使用 * 来查村数据，如果你经常使用 * 查询数据，那么你的水平只能在3000以下。

**常用表达式(`重要`):**
![常用表达式](https://upload-images.jianshu.io/upload_images/13687958-1e44cad566eca1aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**select 5种子句之where匹配**
![select 5种子句之where匹配](https://upload-images.jianshu.io/upload_images/13687958-fc5b2d2a44b962d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6、select查询模型(重要)
> 列是变量
变量可以计算
where是表达式，值为真假 

``` sql
select * from user where id=2;
```
理解: where后面是表达式，返回布尔值。语句根据这个来判断是否匹配
例如: 输入全部还可以写成:
``` sql
select * from user where 1;           /* 1 代表真，0代表假*/
```
为取出的age值 +1 :
``` sql
select id,name,age from user where 1;
```
## 三、奇怪的NULL
> 如果一个字段被设置为不允许为NULL类型，那么会在给他赋值NULL时报错
> NULL字段不等用普通的 = 来查询，那样将会很混乱，结果通常是错误的

查询NULL的方法:
``` sql
select * from user where name is null;          /* 正确 */
select * from user where name is not null;      /* 正确 */

select * from user where name=null;             /* 错误 */
```

**友情链接:**
> [MySQL基础教程2-DQL(select查询)](https://www.jianshu.com/p/d583773bbbd0)
> [MySQL基础教程3-DDL(创建表)](https://www.jianshu.com/p/9122020759d5)
> [MySQL基础教程4-细节知识点](https://www.jianshu.com/p/979c9843101a)

**文集推荐:**
> [Java基础方法集1](https://www.jianshu.com/nb/35411761)
> [Python基础知识完整版](https://www.jianshu.com/nb/35412583)
> [Spring Boot学习笔记](https://www.jianshu.com/nb/35490047)
> [Linux指令进阶](https://www.jianshu.com/nb/35411158)
> [Java高并发编程](https://www.jianshu.com/nb/35701647)
> [SpringMVC基础知识进阶](https://www.jianshu.com/nb/36348245)
> [Mysql基础知识完整版](https://www.jianshu.com/nb/36768953)
> [健康管理系统学习花絮(学习记录)](https://www.jianshu.com/nb/36626677)
> [Node.js基础知识(随手笔记)](https://www.jianshu.com/nb/36852271)
> [MongoDB基础知识](https://www.jianshu.com/nb/36850994)
> [Dubbo学习笔记](https://www.jianshu.com/nb/36474207)
> [Vue学习笔记(随手笔记)](https://www.jianshu.com/nb/35411638)

> 声明：发表此文是出于传递更多信息之目的。若有来源标注错误或侵犯了您的合法权益，请作者持权属证明与本我们(QQ:981086665；邮箱:981086665@qq.com)联系联系，我们将及时更正、删除，谢谢。