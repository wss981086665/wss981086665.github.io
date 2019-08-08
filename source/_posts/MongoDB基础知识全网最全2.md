---
layout: post
title: MongoDB基础知识全网最全2
date: 2019-08-08 01:32:08
categories:
- MongoDB
tags:
- MongoDB
- 数据库
---
## 四、语法
### 1.向集合中插入20000个条数据
``` nosql
# 方法一:    耗时:7.2s    效率太差
for(var i=1; i<20000; i++){
      db.collection.insert({num:i});
} 

# 方法二:    耗时0.4s   效率很好
var arr = [];
for(var i=1; i<20000; i++){
      arr.push({num:i});
} 
db.collection.insert(arr);
```
### 2.查询collection中num大于500的所有数据
``` nosql
db.collection.find({num:{$gt:500}});
```
### 3.查询collection中num大于40,小于50的所有数据
``` nosql
db.collection.find({num:{$gt:40,$lt:50}});
# 注意:开发中绝对不能使用无条件地查询
```
### 4.查询collection中前10条数据
``` nosql
db.collection.find().limit(10);         
# limit()限制获取数据数量的上限 
```
### 5.查询collection中第11到20条数据
``` nosql
db.collection.find().skip(10).limit(10);  
# skip()跳过指定数量的数据
# 注意:skip()与limit()位置可以颠倒,MongoDB会自动调整它们的顺序
```
### 6.查询工资(sal)小于1000或大于2500的员工
``` nosql
db.collection.find({$or:[{sal:{$lt:1000}},{sal:{$gt:2500}}]})
```
### 7.所有工资低于1000的员工加400元的工资
``` nosql
db.collection.updateMany({sal:{$lt:1000}},{$inc:{sal:400}})
```
### 8.查询文档时,让查询结果按sal进行排序 
注: 默认情况根据_id进行排序
``` nosql
db.collection.find({}).sort({sal:1})          #1 代表升序, -1代表降序
# 注意:如果对象中有两个字段,那么先根据第一个字段进行排序。
# 如果遇到第一个字段相同的数据,再对这些数据进行第二个字段的排序
```
## 五、知识点
### 1.文档之间的关系:
> - 一对一 (one to one)     夫妻
> - 一对多/多对一 (one to many)/(many to one)     父母-孩子
> - 多对多 (many to many)

## 六、mongoose 
### 1.Node.js中使用mongoose
#### 1.下载安装mongoose
``` windows
npm install --save mongoose
```
#### 2.在项目中引入mongoose
``` js
var mongoose = require("mongoose");
```
#### 3.连接MongoDB
``` js
mongoose.connect("mongodb://数据库的ip地址:端口号/数据库名",{useNewUrlParser:true})
   注意:如果端口号是默认端口号(27017),则可以省略端口号
   — 监听MongoDB数据库的连接状态
   — 在mongoose对象中,有一个属性叫做connection,该对象表示的就是数据库连接
       通过监视该对象的状态,可以来监听数据库的连接与断开
```
#### 4.断开MongoDB数据库连接
注: 一般不需要调用
注: MongoDB数据库,一般情况下,只连接一次,连接一次以后,除非项目停止服务器关闭,否则连接一般不会断开
``` js
mongoose.disconnect()
// 数据库连接成功的事件
mongoose.connection.once("open",function())
// 数据库断开的事件
mongoose.connection.once("close",function())
```
### 2.mongoose之Model
设:
``` js
var StuModel = mongoose.model("student",stuSchema);
```
注意: 
1.中括号表示可选,没有括号表示必填
2.Model和数据库中的集合一一对应
``` js
Model.find([conditions],[projection],[options],[callback])
// conditions: 查询条件 
// projection: 投影,需要获取到的字段
// options: 查询选项(skip limit)    //第三个参数{skip:3,limit:1}
// callback: 回调函数
```
- **1.StuModel.find({},{name:1,_id:0 },function(err,docs){});**
    返回的是一个数组.docs为查询返回的数据
- **2.StuModel.find({},"name -_id",function(err,docs){});**   
效果与1相同
- **3.StuModel.findById("id",function(err){})**          
返回的是一个对象
- **4.StuModel.update(conditions,doc, [options], [callback])**
- **5.StuModel.updateMany(conditions,doc, [options], [callback])**
- **6.StuModel.updateOne(conditions,doc, [options], [callback])**
- **7.StuModel.remove(conditions, [callback])**
- **8.StuModel.deleteOne(conditions, [callback])**
- **9.StuModel.deleteMany(conditions, [callback])**
- **10.StuModel.count():** 统计文档的数量
### 3.mongoose之Document
注意:Document和集合中的文档一一对应,是Model的一个实例
#### 1.创建一个Document对象(Model实例)
```js
var stu = new StuModel({
     name: "孙悟空",
     age: 19,
     gender: "male",
     address: "花果山"
})
```    
#### 2.保存Document对象
``` nosql
Stu.save(function(err){});
```
#### 3.修改Document对象
``` nosql
(1).doc.age = 18;
    doc.save();
(2).doc.update({$set:{age:28}},function(err){})
(3).doc.set("name","孙悟空");
```
#### 4.删除Document对象
``` nosql
doc.remove(function (err) {})
```
#### 5.获取文档中指定的属性值
``` nosql
(1).var demo = doc.get(“name”);
(2).var demo = doc.name;
```
#### 6.将Document对象转换为JSON数据
``` nosql
doc.toJSON();  
```
#### 7.将Document对象转换为object数据
``` nosql
doc.toObject();  
//注意:转换为普通的js对象以后,所有的Document对象的方法或属性都没有用啦
```

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