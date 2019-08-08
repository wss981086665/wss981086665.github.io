---
layout: post
title: MySQL基础教程3-DDL(创建表)
date: 2019-08-07 20:52:00
categories:
- MySQL
tags:
- MySQL
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Mysql并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

# DDL-创建表
> - 所谓建表，就是一个声明字段的过程
> 列选什么类型，列选什么属性

**简单建个表:**
``` sql
create table people(
     -> age int,
     -> name varchar(10)
     -> );
```
**建表语句:**
![建表语句](https://upload-images.jianshu.io/upload_images/13687958-09b243e830d5b0c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 一、列
> **列的类型:**
> - 数值型---整形、型，浮点型，定点型
> - 字符串型---char，varchar，text
> - 日期时间类型---2020-12-30 23:59:59

### 1.1.整型列
**(1).各整型类型所占字节大小:**
![各整型类型所占字节大小](https://upload-images.jianshu.io/upload_images/13687958-0056674a8240bd07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**(2).整型列可选参数:**
- **unsigned:** 无符号，列的值从0开始，不为负
- **zerofill:** 适用于 学号、编码 等，固定宽度的数字，可以用0填充至固定宽度
学号-->1-->0001
学号-->123-->0123
思路: zerofill填充至多宽？M
注意: zerofill属性默认决定列为unsigned类型
- **M:** 为zerofill 指定填充的宽度。只使用M没有任何意义，必须配合zerofill使用
注意: M的数值不是为了限制数值宽度，只是为了当数值宽度不到M时，为其填充至宽度为M

**例:**
``` sql
/* 为user表添加带有unsigned属性的number字段 */
alter table user add number tinyint unsigned;

/* 为user表添加带有zerofill属性的number字段，并为其指定宽度 */
alter table user add number tinyint(5) zerofill;
```
### 1.2.浮点列与定点列
- **flocat(M,D): 浮点型**
**`M`**是精度,即总位数
**`D`**是标度,即小数点后面的位数
- **double: 浮点型**
- **decimal: 定点型**
**`float/double`**浮点型数据会有精度损失,**`decmail`**定点型,更精确

**float/double官方文档:**
![float/double官方文档](https://upload-images.jianshu.io/upload_images/13687958-16a07e2ead04af4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 1.3.字符型
**MySQL字符串类型:**
- Char: 定长类型
char型，传入数据如果不够M个字符，内部用空格补齐，取出时再把**`右侧空格`**删掉。这意味着:**`如果数据本身右侧有空格，将会丢失`**
- Varchar: 变长类型

> **注意:**char(M)限制的是字符，不是字节。即char(2) charset utf8，能存两个utf8字符，比如中国;
> **char与varchar型的选择原则:
> - 1.空间利用率：
> 四字成语表:char(4)；
> 微博个人简介140字:varchar(140)
> - 2.速度：
> 用户名: char
 
**Char与Varchar:**
![MySQL字符串类型](https://upload-images.jianshu.io/upload_images/13687958-294c0ca607b85089.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**Char右侧空格丢失:**
注: 从截图中可以看出，最后输出的数据中的char型数据丢失了右侧的空格
![Char右侧空格丢失](https://upload-images.jianshu.io/upload_images/13687958-df43fa941f6f348b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**其他类型的字符类型及容量:**
> - **Text**: 文本类型，可以存比较大的文本段，搜索速度慢。因此，如果不是特别大的内容，建议用char、varchar来代替。test不用默认值(加了也没用) 
> - **Blob**:二进制类型，用来存储图像、音频等二进制信息。
> **意义:**2进制，0-255都有可能出现。Blob在于防止因为字符集而导致信息丢失的问题。比如: 一张图片中有0xFF字节，这个在ascii字符集中认为非法，在入库的时候被过滤了。
> - **Enum:**枚举类型。意思是值是定义好的，就在几个枚举范围内。
> 例: gender('男','女')；insert时只能选"男","女"。
> - **Set:**同Enum。但insert时可同时选多个值。

![其他类型的字符类型及容量](https://upload-images.jianshu.io/upload_images/13687958-1902abf72caee170.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 1.4.日期时间类型
![日期时间类型](https://upload-images.jianshu.io/upload_images/13687958-e033035cf27b686b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 二、列的默认值
> 列的默认值
> - 1.NULL 查询不便
> - 2.NULL的索引效果不高
> 所以，实用中要避免列的值为NULL
> - 如何避免--声明列 :
**`NOT NULL default 默认值`**

## 三、主键与自增
- **主键:**primary key，此列不重复，能够区分每一行

``` sql
/* 第一重指定方式 */
create table t1(
     -> id int primary key,
     -> name char(2)
);
/* 第一重指定方式 */
create table t2(
     -> id int,
     -> name char(2),
     -> primary key(id)
);
```
- **自增`(auto_increment)`:** 一张表只能有1列为auto_increment，且此列必须加索引(index/key)
一般实用中自增与主键搭配使用

``` sql
create table t3(
     -> id int auto_increment,
     -> name char(2),
     -> key id(id)
);
```
- **表设计案例:某社交网站**

> **建表秘诀:**
> 1.定长与变长分离
> 2.常用与不常用分离

![表设计案例](https://upload-images.jianshu.io/upload_images/13687958-069982d22e3e5968.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上面表设计的并不是很好，还可以优化
**分析:**张这表除了username/intro列之外，每一行都是定长的，我们不妨让其所有列都定长。可以极大地提高查询速度。
![表设计优化](https://upload-images.jianshu.io/upload_images/13687958-49a5f294513de090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**MySQL建立上述表表指令:**
 ``` sql
create table regist3(
     -> id int unsigned primary key auto_increment,
     -> username char(10) not null default ' ',
     -> gender tinyint not null default 0,
     -> weight tinyint unsigned not null default 0,
     -> Birth Date not null default '0000-00-00',
     -> Salary Decimal(8,2) not null default '000000.00',
     -> lastlogin int unsigned not null default 0
);

create table regist1(
     -> id int unsigned primary key auto_increment,
     -> username char(10) not null default ' ',
     -> intro varchar(1500) not null default ''
);
```
**效果图:**
![效果图](https://upload-images.jianshu.io/upload_images/13687958-32582aed59c5353e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 四、列的增删改
### 4.1.添加列
``` sql
/*格式:*/
alter table 表名
     -> add 列名 列类型 列属性... 
注意: 默认将该列添加到表的最后

/*实例1:*/
alter table student
     -> add height tinyint unsigned not null default 0;

/*实例2: 添加到weight列后面*/
alter table student
     -> add height tinyint unsigned after weight;
```
### 4.2.删除列
``` sql
alter table student
     -> drop column height;
```
### 4.3.修改列
``` sql
/*修改列名并修改该列的属性*/
alter table student
     -> change height shengao smallint;

/*修改列的属性*/
alter table student
     -> change shengao tinyint;
```
## 五、表管理语句
### 5.1.查看所有表
``` sql
show tables;
```
### 5.2.查看表结构
``` sql
desc 表名/视图名;
```
### 5.3.查看建表过程
``` sql
/*查看建表过程*/
show create table 表名;

/*查看建视图过程*/
show create view 视图名;
```
### 5.4.查看表详细信息
``` sql
/*查看所有表详细信息*/
show table status;

/*查看某张表详细信息*/
show table status where name='表名';

/*竖列显示表信息*/
show table status \G;
```
### 5.5.删除表
``` sql
/*删除表*/
drop table 表名;

/*删除视图*/
drop view 视图名;
```
### 5.6.为表改名
``` sql
rename table oldName to newName;
```
### 5.7.清空表数据
``` sql
truncate 表名;
/*相当于删除表，再重建*/
```

**友情链接:**
> [MySQL基础教程全网最全1(基本指令操作)](https://www.jianshu.com/p/dbb98595da53) 
> [MySQL基础教程2-DQL(select查询)](https://www.jianshu.com/p/d583773bbbd0)
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