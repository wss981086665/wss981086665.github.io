---
layout: post
title: Node.js学习笔记3-mongoose
date: 2019-08-07 21:06:55
categories:
- Node.js
tags:
- Node.js
- 后端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Node.js并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.认识mongoose
### 1.1.下载安装mongoose
``` js
npm install --save mongoose
```
### 1.2.在项目中引入mongoose
``` js
var mongoose = require("mongoose");
```
### 1.3.连接MongoDB
``` js
mongoose.connect("mongodb://数据库的ip地址:端口号/数据库名", {useNewUrlParser:true})
```
**注意:**
- 如果端口号是默认端口号(27017),则可以省略端口号
- 监听MongoDB数据库的连接状态
- 在mongoose对象中,有一个属性叫做connection,该对象表示的就是数据库连接
- 通过监视该对象的状态,可以来监听数据库的连接与断开

### 1.4.断开MongoDB数据库连接(一般不需要调用)
- MongoDB数据库,一般情况下,只连接一次,连接一次以后,除非项目停止服务器关闭,否则连接一般不会断开

``` js
mongoose.disconnect()

// 数据库连接成功的事件
mongoose.connection.once("open",function())

// 数据库断开的事件
mongoose.connection.once("close",function())
```
- **Schema**
- **Model**
- **Document**

**连接MongoDB数据库:**
``` js
//引入
var mongoose = require("mongoose")
//连接数据库
mongoose.connect("mongodb://127.0.0.1/mongoose_test", {useNewUrlParser:true})

mongoose.connection.once("open",function () {
    console.log("数据库连接成功~~~")
})
```

## 2.mongoose测试
- 通过mongoose操作MongoDB非常简单，首先获取mongoose.Schama，然后创建Schema（模式）对象，然后创建Model，最后向数据库中插入数据。

### 2.1.简单使用mongoose
``` js
var mongoose = require("mongoose")

mongoose.connect("mongodb://127.0.0.1/mongoose_test", {useNewUrlParser:true})

mongoose.connection.once("open",function () {
    console.log("数据库连接成功~~~")
})

//将mongoose.Schema赋值给一个变量
var Schema = mongoose.Schema;

//创建Schema(模式)对象
var stuSchema = new Schema({
    name:String,
    age:Number,
    gender:{
        type:String,
        default:"female"   //设置字段类型并设置默认值
    },
    address:String
})

//通过Schema来创建Model
//Model代表的是数据库中的集合,通过Model才能对数据库进行操作
//mongoose.model(modelName,Schema);
//modelName 就是映射的集合名。mongoose会自动将集合名变成数
var StuModel = mongoose.model("student",stuSchema);

//向数据库中插入一个文档
//StuModel.create(doc,function(err){});
StuModel.create({
    name:"逍遥子",
    age:18,
    gender:"female",
    address:"南山"
},function (err) {
    if(err){
        console.log("插入文档失败");
        return;
    }else {
        console.log("插入成功");
    }
})
```

### 2.2.另一个Schema（模式）
``` js
//创建Schema(模式)对象
var qusSchema = new Schema({
    author:String,
    topic:String,
    content:String,
    openid:String,
    avatural:String,
    subject:String,
    nickName:String,
    time:String,
    image:{
        type:String,
        default:"defaultimg.jpg"
    },
})
```

### 2.3.Document
``` js
//创建Schema(模式)对象
var stuSchema = new Schema({
    name:String,
    age:Number,
    gender:{
        type:String,
        default:"female"   //设置字段类型并设置默认值
    },
    address:String
})

var StuModel = mongoose.model("student",stuSchema);

//创建一个Document
var stu = new StuModel({
    name: "孙悟空",
    age: 19,
    gender: "male",
    address: "花果山"
})

stu.save(function (err) {
    if(!err){
        console.log("保存成功");
    }
});
```


## 2.MongoDB配置
**mongodbset.js**
``` js
var mongoose = require("mongoose")

mongoose.connect("mongodb://127.0.0.1/fengwoForum", {useNewUrlParser:true})

mongoose.connection.once("open",function () {
    console.log("数据库连接成功~~~")
})

//将mongoose.Schema赋值给一个变量
var Schema = mongoose.Schema;

exports.Schema = Schema;
exports.mongoose = mongoose;
```

**testMap.js**
``` js
var Schema = require("../module/mongodbset.js").Schema
var mongoose = require("../module/mongodbset.js").mongoose

//创建Schema(模式)对象
var qusSchema = new Schema({
    author:String,
    topic:String,
    content:String,
    openid:String,
    avatural:String,
    subject:String,
    nickName:String,
    time:String,
    image:{
        type:String,
        default:"defaultimg.jpg"
    },
})

var QusModel = mongoose.model("question",qusSchema);

module.exports = QusModel;
```

**testController.js**
``` js
var queModel = require("../database-map/testMap.js");

exports.addQuestion = function (callback) {
    queModel.create({
        author:"王顺顺666",
        topic:"从前有个山",
        content:"山上有个寺庙!",
        openid:"10000001",
        avatural:"https://www.xztywss.top/img/aaa.png",
        subject:"大物2",
        nickName:"庙里有个老和尚",
        time:"2019/1/5 14/13",
        image:"defaultimg.jpg"
    },function (err) {
        if(err){
            callback(err);
            return;
        }else {
            return;
        }
    })
}

exports.getQuestion = function (callback) {
    queModel.find({},function(err,docs){
        if(err){
            callback(err,null)
            console.log(err);
            return;
        }
        callback(null,docs);
        return;
    })
}
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
