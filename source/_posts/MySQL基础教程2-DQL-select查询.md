---
layout: post
title: MySQL基础教程2-DQL(select查询)
date: 2019-08-07 20:51:17
categories:
- MySQL
tags:
- MySQL
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Mysql并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

# select查询
## 序、简单查询事项
**1.避免重复数据查询**
``` sql
select distinct name from user;
```

**2.实现数学运算数据查询**
``` sql
select age*10 from user;

给运算过的数据设置显示名称:
select num*10 as salary from user;
```

**3.条件查询**
``` sql
单条件查询：
select * from user where name='xqz';

多条件查询：
select * from user where name='xqz' && age=19;
```

** 4.符合范围的记录查询**
``` sql
select name age
    from user 
        where age between 18 and 20
```

**5.带IS NULL关键字的空值查询**
``` sql
空值数据查询：
SELECT NAME AGE
    FROM USER
        WHERE AGE IS NULL;

非空值数据查询：
... IS NOT NULL;
```

## 一、group分组与统计函数
``` sql
理解: 根据某一指定属性对前面select之后的运算进行分组执行
例如: select age,avg(id) from user group by age;
      解释:分别求出各年龄(age)段的id平均值
```
**本小节知识清单**:
> - **max()**
> - **count()**
> - **avg()**
> - **min()**
> - **sum()**
> - **round()**

``` sql
select max(age) from user;          /* 获得所有用户最大的年龄 */
select count(age) from user;        /* 获得所有用户的数量 */
select avg(age) from user;          /* 求出所有用户的年龄平均值 */
select min(age) from user;          /* 获得所有用户最小的年龄 */
select sum(age) from user;          /* 求出所有用户的年龄之和 */
select sum(id*age) from user;       /*求出所有用户的 id*age 值的和*/
```
**round()函数**
```sql
1. round(x,d)  ，x指要处理的数，d是指保留几位小数
这里有个值得注意的地方是，d可以是负数，这时是指定小数点左边的d位整数位为0,同时小数位均为0；
2、round(x)  ,其实就是round(x,0),也就是默认d为0；
下面是几个实例
1. 查询: select round(1123.26723,2);
     结果：1123.27
2、查询: select round(1123.26723,1);
     结果: 1123.3
3、查询: select round(1123.26723,0);
     结果：1123
4、查询: select round(1123.26723,-1);
     结果: 1120
5、查询: select round(1123.26723,-2);
     结果：1100
6、查询: select round(1123.26723);
     结果：1123
```
### 根据年龄分组计算id的平均值
``` sql
select age,avg(id) from user group by age;
```
**效果:**
![效果](https://upload-images.jianshu.io/upload_images/13687958-cd42872ebe6d1235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 查询每个年龄下的用户的数量
``` sql
select age,count(*) from user group by age;
```
**效果:**
![效果](https://upload-images.jianshu.io/upload_images/13687958-bb155b770e79c1be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 查询每个年龄下 id 最高的用户
``` sql
select age,max(id) from user group by age;
```
## 二、having筛选
### (1).查询age-id>15的用户
``` sql
select name,age-id from user where (age-id)>15;
select name,(age-id) as demo from user where demo>15;        /* 错误 */
select name,(age-id) as demo from user where 1 having demo>15;   /* 正确 优化 */
注意：博主在使用index作为替代变量时执行语句会出错，可能index为MySQL保留字，望读者注意
```
### (2).where-having-group综合练习题
**有如下表及数据**
name|subject|score
--|--|--
张三|数学|90
张三|语文|50
张三|地理|40
李四|语文|55
张三|数学|45
张三|数学|30
**要求: 查出两门及两门以上不及格者的平均成绩:**
``` sql
select name,sum(score<60) as gk,avg(score) as pj
     -> from  result group by name
     -> having gk>=2;
```
## 三、order by排序
> - **降序: desc**
> - **增序: asc [默认asc]**
> - **多列排序: 逗号隔开，依次添加条件即可**

``` sql
/* 将用户按照age增序排序 */
select * from user order by age;
/* 将用户按照age进行降序排序 */
select * from user order by age desc;
/* 先按照age进行增序排序，再为分组好的age序列根据id进行降序排序；*/
select * from user order by age asc,id desc;
```
## 四、limit语法
``` sql
找出年龄前三名的同学
select * from user order by age limit 0,3;
0: 偏移量。默认是0，可以省略
3: 取值个数
```
> - **`LIMIT`**可以用来实现分页功能
> - 但是数据太大的时候limit表现的并不是很完美,这里链接以下掘金里的一个文章，有兴趣的读者可以去看一下: [MySQL之LIMIT性能问题](https://juejin.im/post/5cd2d57951882540d928c2be)

**练习:**
**利用where,group by,having，order by,limit 其中几种的组合指令查询每个age段里id最大的用户**
**注意:** 如果选择组合使用这几种，那么他们的顺序必然是固定的，不可颠倒
``` sql
/* 利用子查询进行查找，具体子查询详情在---where型子查询---中再讲 */
select age,id,name from user where id in (select max(id) from user group by age);
```
## 五、where型子查询
**查出年龄最大的用户:**
``` sql
/* 排序法(浪费资源) */
select name,age from user order by age limit 0,1;
/* 下面一条是博主尝试的一个****错误的思路****，无法执行 */
select id,name max(age) as max from user where 1 having age=max;
/* 利用子查询(正确、高效) */
select name,age from user where age=(select max(age) from user);
```
> 注意: 子查询语句只能有一个返回结果，有多个返回结果不然会报错。

## 六、from型子查询
> 理解: select 语句返回的结果也是一张表，大胆的把select的结果当作另一个select语句所要查询的表即可

**查询年龄为19中id最大的用户:**
``` sql
select id,name,age from (select * from user where age=19) as tmp order by id desc limit 0,1;
```
**查询出编号为19的商品所在的栏目的名称:**
**注意:** 此表中只包含栏目的编号，并不包含栏目的名称，栏目的编号与名称对应关系在另外一张表中.可以用**`子查询`**或下面的**`连接查询`**
``` sql
/* 子查询 */
select name from xxx1 where index=(select index from xxx2 where id =19);
xxx1: 栏目的编号与名称对应关系表
xxx2: 商品表

/* 连接查询 */
略
```
## 七、exists型子查询
> 理解: 如果子查询有结果，主查询就继续根据结果去查询；如果子查询没有结果，主查询就不继续根据此结果查询

**用exists型子查询，查出所有里面有商品的栏目
``` sql
select * from category 
     where exists (select * from goods where goods.cat_id=category.cat_id)
/* c此语句会将category表中的所有 cat_id 与进行查询 */
```
## 八、新手1+ N查询(反例)
``` php
$sql = 'select cat_id,shop_price from goods where shop_price>2000';
$rs = mysql_query($sql);

$data = array();
while() {
     $row...
}

$data = array(7条商品);

foreach($data in $goods) {
     $sql = 'select cat_name from category where cat_id = $goods['cat_id']';
}
```
- **即: 一条语句--->N条查询**
- **这种写法是非常低级的写法**
- **可以用`内联查询`解决这种问题**

## 九、内联查询
**语法:**
``` sql
select xxxx from
     -> table1 inner join table2 on table1.xx=table2.xx;
```
有这样两张表:(假如说boy与girl表中hid匹配上的用户就是搭档关系)
![boy and girl](https://upload-images.jianshu.io/upload_images/13687958-a8b0ec05231d4b90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
要求: 将这两张表hid相同的人(name)组合起来(输出所有搭档组合)
``` sql
select boy.hid,boy.bname,girl.hid,boy.gname
     -> from
     -> boy inner join girl 
     -> on boy.hid=girl.hid;
```
**输出结果:**
![输出结果](https://upload-images.jianshu.io/upload_images/13687958-b2fffa14439af314.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 十、左连接及右连接查询
> - 理解:还是上面的例子。输出所有boy，有搭档girl匹配显示上，没有搭档就显示为NULL
> - 左连接即以左边数据为准查询右边数据，查不到补NULL

**(1).左连接:**
``` sql
select boy.hid,boy.bname,girl.hid,boy.gname
     -> from
     -> boy left join girl
     -> on boy.hid=girl.hid;
```
**左连接查询结果:**
![左连接查询结果](https://upload-images.jianshu.io/upload_images/13687958-f20b44990ae92d34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**(2).右连接**
``` sql
select boy.hid,boy.bname,girl.hid,boy.gname
     -> from
     -> boy right join girl
     -> on boy.hid=girl.hid;
```
**右连接查询结果:**
![右连接查询结果](https://upload-images.jianshu.io/upload_images/13687958-4b687a9c2d82f2ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**(3).练习:查询所有商品的商品名，栏目名，价格**
注: 栏目名在另外一张表中，因此要进行连接查询
``` sql
select goods_id,cat_name,goods.cat_id,shop_price
     -> from
     -> goods left join category on goods.cat_id=category.cat_id;
```
**(4).练习:查询所有第四栏目下所有商品的商品名，栏目名，价格**
``` sql
select goods_id,cat_name,goods.cat_id,shop_price
     -> from
     -> goods left join category on goods.cat_id=category.cat_id
     -> where goods.cat_id=4;
```
**(5).一道面试题**
有如下两张表:
![两张表结构](https://upload-images.jianshu.io/upload_images/13687958-528fdb453f51fa7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> - Match的hosteamID与guestTeamID都与Team中的teamID关联
> - 题目: 查出2006-6-1到2006-7-1之间举行的所有比赛，并且用以下形式列出:
> 拜仁 2：0  不来梅   2006-6-21

``` sql
select mid,t1.tname as hname,mres,t2.tname as gteam,matime
     -> from
     -> m inner join t as t1 on m.hid=t1.tid inner join t as t2 on m.gid=t2.tid
     -> where matime between '2006-06-01' and '2006-07-01';
```
**查询结果:**
![查询结果](https://upload-images.jianshu.io/upload_images/13687958-309f92e7acb85893.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 十一、union查询
> 使用场景: 2条语句，各自的where条件非常复杂，可以简化成简单条件，再union

**例:找出年龄(age)为18和20的用户**
``` sql
select name,age from user where age=18
     -> union
     -> select name,age from user where age=20;
``` 
> - union语句必须满足一个条件:各语句取出的列数相同
> - 列名称不一定要相等，列名称会使用第一条sql的列名为准
> - `注意: 使用union时，完全相等的行将会被合并。`
> `并且: 合并是比较耗时的操作。使用'union all'避免合并`
> union的子句中，不用谢order by。sql语句合并后得到的结果可以oeder by。

**例题:**
有如下两张表，将两张表中的数num加起来。
![image.png](https://upload-images.jianshu.io/upload_images/13687958-ceade2eb619a7fcc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
``` sql
select id,sum(num)
     -> from
     -> (select * from a
     -> union all
     -> select * from b) as tmp
     -> group by id;
```
## 十二、查询练习
### user表中的数据(练习用):
![image.png](https://upload-images.jianshu.io/upload_images/13687958-225aa240e9b01f57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 1.基础查询 where 的练习
**查询满足以下条件的商品:**
#### 1.1.主键为 3 的用户
``` sql
select * from user where id=3;
```
#### 1.2.年龄不是 19 的用户
``` sql
1. select name,age from user where age!=19;
2. select name,age from user where age<>19;
```
#### 1.3.年龄大于19的用户
``` sql
select name,age from user where age>19;
```
#### 1.4.年龄低于或等于19的用户
``` sql
select name,age from user where age<=19;
```
#### 1.5.年龄为19或20的用户(不许用or)
``` sql
/*  1. select * from user where age=19 or age=20; */
2. select * from user where age=19 || age=20;
3. select * from user where age in (19,20);
```
#### 1.6.找出18<=年龄<=19的用户(不许用and)
``` sql
/* 1. select name,age from user where age>=18 and age<=19; */
2. select name,age from user where age between 18 and 19;
```
#### 1.7.找出年龄不是18且不是19的用户(and或not in分别实现)
``` sql
1. select name,age from user where age!=18 and age!=19;
2. select name,age from user where age not in (18,19);
```
#### 1.8.找出年龄大于17且小于19，或者年龄大于20小于22的用户
``` sql
select name,age from user where (age>17 and age<19) or (age>20 and age<22);
```
#### 1.9.找出年龄为19岁并且id<2或>4,并且name为'zk'的用户
``` sql
select * from user 
     where age=19 and (id<2 or id>4) and name='zk';
/* 括号是必要的，因为不加括号就会使and先执行 */
```
#### 1.10.找出name为'zk'的用户
(注意:不存在'zk'在这个用户，但是他有两个儿子。此处假设有father这个表项)
``` sql
select * from user where name in ('zk1','zk2');
```
#### 1.11.找出name以'w'开头的用户
``` sql
1. select name,age from user where name like 'w%';

/* 匹配以name'w'开头并且后面有两个任以字符的用户 */
2. select name,age from user where name like 'w__';   
```
#### 1.12.找出年龄在18到20之间，并且id>3,name以'z'开头的用户
``` sql
1. select name,age from user where age>18 and age<20 and id>3 and name like 'z%';
2. select name,age from user where age between 18 and 20 and id>3 and name like 'z%';
```
### 2.一道面试题
> 有如下表(girl)和数组
> 把num处于[20，29]之间的数值改为20
> 把num处于[30，39]之间的数值改为30，

num|
--|
3|
12|
15|
25|
23|
29|
34|
37|
32|
``` sql
update girl set num=floor(num/10)*10 where num between 20,30;
```
### 3.练习题
> 把user表中用户名为'慕容xxxx'的用户，改为'诸葛xxxx'
> 提示: 大胆的把列看成变量，参与运算，甚至调用函数来处理。
> substring(), concat()
``` sql
update user set name=concat('诸葛',substring(name,3)) where name like '慕容____';
```

**友情链接:**
> [MySQL基础教程全网最全1(基本指令操作)](https://www.jianshu.com/p/dbb98595da53) 
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
