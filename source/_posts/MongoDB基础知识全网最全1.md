---
layout: post
title: MongoDB基础知识全网最全1
date: 2019-08-08 01:31:55
categories:
- MongoDB
tags:
- MongoDB
- 数据库
---
## 一、细节
### 1. 更改端口号
``` sql
命令后加上 –port 10086
```
### 2.更改数据存放地址
``` nosql
--dppath C:\Users\MongoDB\data\db
```
### 3.启动
``` sql
net start MongoDB
mongod –dbpath 数据库路径 –port 端口号
```
### 4.启动与打开
> 启动mongodb: 打开cmd窗口，输入mongod启动服务器
连接mongodb: 打开cmd窗口，输入mongo连接mongodb启动客户端，出现 >

### 5.将MongoDB设置为系统服务
可以在后台启动，不需要每次都手动
> - 先在data文件下创建一个新文件夹log（用来存放日志文件）
> - 在Mongodb新建配置文件mongo.config
> - 用记事本打开mongo.config  ，并输入：
dbpath=MongoDB安装目录\data\db
 logpath= MongoDB安装目录\data\log\mongo.log
> - 用管理员身份打开cmd:
> - 配置windows服务：
cmd先跳转到 MongoDB安装目录\bin目录下。
输入：mongod --config "D:\software\Mongodb\mongo.config" --install --serviceName "MongoDB"
即根据刚创建的mongo.config配置文件安装服务，名称为MongoDB。

## 二、三个重要概念
### 数据库( database ): 
数据库是一个仓库，在仓库中可以存放集合
### 集合( collection ): 
集合类似于数组，在集合中可以存放文档
### 文档( document ): 
> - 文档是数据库中最小单位，我们存储和操作的内容都是文档
> - 在MongoDB中，数据库和集合都不需要手动创建。
> - 当创建文档时，如果文档所在的集合或数据库不存在就会自动创建数据库和集合
## 三、指令
### 3.1.基本指令
**注**: 以下指令中collection均指集合的名称:
``` sql
1. show dbs (show databases):        #显示所有数据库
2. use test:      #表示进入test这个数据库
3. db:       #显示当前所在的数据库的名称
4. show collections:       #显示数据库所有的集合
```
### 3.2.数据库的CRUD(增删改查)的操作:
- **5.db.collection.insert(doc):**  
> 向集合中插入一个文档(collection是集合的名称)(参数是数组即插入多个。插入时会自动生成一个_id,这个值也可以自己设置)

- **6.db.collection.insertOne():**      
其实和insert一样，只是看起来更清晰
作用: 插入一个文档
- **7.db.collection.insertMany():**    插入多个文档
- **8.db.collection.find():**  
> - 查询集合中所有的文档,没有参数表示查询所有文档。可以传入对象作为参数。返回的是一个数组，后面加上`[i]`表示数组里的第i个文档。加上`.length()`或`.count()`获取该数组的长度
例如: `db.collection.find({name:"demo"})`;表示查询所有`name`为`demo`的文档
> - 在查询时,可以在第二个参数的位置设置查询结果的投影。例`({},{sal:1})`即只显示查询结果`sal`属性(`_id`属性默认显示),可以设置不显示`{_id:0}`
> - 注意:`MongoDB`支持直接通过内嵌文档的属性进行查询,如果要查询内嵌文档则可以通过 . 的形式进行匹配。如果一个内嵌文档的值是一个数组,那么只要数组里含有此参数值就可以匹配上。而且,此属性名必须使用引号,说明他是一个表达式

- **9.db.collection.findOne({name:"demo"}):**
> 查询符合条件的第一个文档，返回的是对象，可以直接在后面加上`.name`获取name属性的值
- **10.db.collection.update(查询条件,新对象):**     //替换
> - update()默认会直接用新对象替换掉匹配到的对象;如果只是需要修改部分属性,而不是整个替换,就要使用”修改操作符”。详情看*11
> - 注意:如果匹配到的值是一个数组,那么就可以用$set替换$push实现往数组里添加此参数,且允许重复。$addToSet向数组中添加一个新元素,但是如果数组中含有此元素,就不会重复添加(不允许重复)。
- **11.db.collection.update:**
使用方法:
``` nosql
db.collection.update(
      查询条件,
      { $set:{
          Name:”demo”
      } }
)
```
> - $set 表示只修改文档中指定的属性
> - $unset 表示删除文档中的指定属性,值任意取都行
> - 如果想修改多个，就需要在添加一个对象参数,并设置multi:true
- **12.db.collection.replaceOne():**
- **13.db.collection.replaceMany():**
- **14.db.collection.remove(对象): **       //删除
> - 删除根据对象匹配到的所有文档(默认删除所有匹配到的文档),如果条件对象为空,就会删除所有数据
> - 第二个参数如果是true,就只会删除一个,默认为false(删除所有匹配到的文档)
- **15.db.collection.drop():**  
                  -- 直接删除集合,性能比remove()更好
- **16.db.dropDatabase():**
                -- 直接删除数据库
- **17.db.collection.deleteOne():**
- **18.db.collection.deleteMany():**
-- 一般开发中不会删除数据,会为数据添加一个字段表示是否被删除,查询的时候在条件对象中排除已经被删除的数据

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
