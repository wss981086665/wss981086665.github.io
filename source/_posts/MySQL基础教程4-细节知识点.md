---
layout: post
title: MySQL基础教程4-细节知识点
date: 2019-08-07 20:52:59
categories:
- MySQL
tags:
- MySQL
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Mysql并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

# 细节知识点
## 一、视图(view)
- view又被称为虚拟表，view是sql语句的查询结果
- 有什么用？
**1.权限控制时可以使用:**
比如某几个列，允许用户查询，其他列不允许。可以通过视图，开放其中一列或几列，起到权限控制的作用
**2.简化复杂的查询:**
查询每个栏目下商品的平均价格，并按平均价格排序，查出平均价格前三的商品:

``` sql
/*创建视图*/
create view v3 as 
     -> select cat_id,avg(shop_price) as pj 
     -> from goods group by cat_id;

/*查询平均前三*/
select * from v3 order by pj desc limit 0,3;
```
- **视图能不能更新删除:**
答: 如果视图中每一行是与物理表中一一对应的，则可以。view的行如果是由物理表中多行经过计算得到的结果，则不可以更新

- **视图的algorithm**
**`视图放哪了？(存储的都是语句)`**
1.对于简单查询形成的`view`，再对`view`查询时，如`where`、`order`等等，可以把建视图语句+查视图语句====合并成==>查物理的语句。这种视图的算法叫`merge(合并)`
2.也有可能，视图语句本身比较复杂，很难再和查询试图的语句合并，mysql可以先执行视图的创建语句，把结果集形成内存中的临时表，然后再去查临时表

**CREATE VIEW语法:**
![CREATE VIEW语法](https://upload-images.jianshu.io/upload_images/13687958-cfa4ccf82232d987.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 二、引擎的概念
- 理解引擎的概念

![引擎的概念](https://upload-images.jianshu.io/upload_images/13687958-b8b43ee3a68726e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 知道常用引擎的区别

![常用引擎的区别](https://upload-images.jianshu.io/upload_images/13687958-4a0b7b24a6c170e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 三、字符集与乱码
**查看字符集:**
``` sql
show variables like '%charact%';
```
**字符集:**
![字符集](https://upload-images.jianshu.io/upload_images/13687958-c04ebb3bc4f9e369.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**设置字符集:**
``` sql
/*设置输入字符集*/
set character_set_client=gbk;

/*设置连接字符集*/
set character_set_connection=gbk;

/*设置输出字符集*/
set character_set_result=gbk;
```
## 四、索引
### 4.1.索引的概念
- 索引是数据的目录，能快速定位行数据的位置
- 缩印提高了查询速度，降低了增删改的速度，并非加的越多越好
- 一般在查询频繁的列上加。而且在重复度低列上加效果好
- 索引的类型:
**`key:`** 普通索引
**`unique:`** 唯一索引
**`primary key:`** 主键索引
**`fulltext:`** 全文索引（中文环境下，全文索引无效，要分词+索引才可以，一般使用第三方解决方案，如`sphinx`）
- 索引长度: 建索引时，可以只索引列的前一部分的内容，比如: 前十个字符
如: `key email(email(10))`
- 多列索引: 就是把两列或多列的值看成一个整体，然后建立索引
**实例:**
``` sql
create table student(
     -> xing char(2),
     -> ming char(10),
     -> key name(xing,ming)
)
```
- 冗余索引: 就是在某个列上，可能存在多个索引
比如: name(xing,ming),    ming(ming)

### 4.2.索引操作
``` sql
/*查看索引*/
1. show index from 表名
2. show create table 表名

/*删除索引*/
1. alter table 表名 drop index 索引名
2. drop index 索引名 on 表名

/*添加索引*/
alter table 表名 add [index/unique] 索引名(列名)

/*添加主键索引*/
alter table 表名 and primary key(列名)；

/*删除主键索引*/
alter table 表名 drop primary key;
```
## 五、常用函数
### 5.1.数学函数
![数学函数](https://upload-images.jianshu.io/upload_images/13687958-97029c68ef791106.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 5.2.聚合函数
![聚合函数](https://upload-images.jianshu.io/upload_images/13687958-a4301e40c8eb0ce5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 5.3.字符串函数
![字符串函数](https://upload-images.jianshu.io/upload_images/13687958-f7ea1b876a3e6833.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 5.4.日期与时间函数
![日期与时间函数](https://upload-images.jianshu.io/upload_images/13687958-838ba2e592b7a2c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 5.5.加密函数
![加密函数](https://upload-images.jianshu.io/upload_images/13687958-5d5f43293ebdfc0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 六、事务
**开始一个事务:**
``` sql
start transaction;
```
**结束事务**
``` sql
commit;
```
**事务回滚**
``` sql
/*结束事务并使数据返回事务开始之前的状态*/
rollback;
```
**分析**
- **隔离性:** 事务开始之后，其他用户不会看到其中的变化，知道事务结束，数据的变化才会被其他用户看到
- **原子性:**数据的状态只有可能是修改之前的状态，或者是提交之后的状态，其他用户不会看到正在修改的数据状态
- **一致性:**事务前后的数据具有一致性
- **持久性:**一旦`commit`之后，事务就无法回滚(rollback)

**结束语:**
> 至此，MySQL基础知识介绍基本完毕。但是，纸上谈兵一场空，读者在学习完基础知识之后就应该去做一些MySQL练习题以巩固所学到的知识。、
去哪找练习题？百度搜索
> 这里给推荐一个CSDN用户**`我疯`**的练习题(有点难度):[链接](https://blog.csdn.net/qq_41936662/article/details/80393172)

**友情链接:**
> [MySQL基础教程全网最全1(基本指令操作)](https://www.jianshu.com/p/dbb98595da53) 
> [MySQL基础教程2-DQL(select查询)](https://www.jianshu.com/p/d583773bbbd0)
> [MySQL基础教程3-DDL(创建表)](https://www.jianshu.com/p/9122020759d5)

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
