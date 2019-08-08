---
layout: post
title: Node.js学习笔记4-模块、POST请求（文件上传）
date: 2019-08-07 21:07:05
categories:
- Node.js
tags:
- Node.js
- 后端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Node.js并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.模块
### 1.1.示例：time-modules模块
**使用**
``` js
//test2:npm引入模块使用

//需要使用一个日期
const dateTime = require('date-time');

var time = dateTime();

console.log(time);

//npm init 会将项目信息打包成一个json文件,其中dependencies中包含的是项目的所有依赖
//npm install会安装该项目中所有的依赖
```

### 1.2.示例：self-modules模块
#### 1.2.1.创建People类
``` js
function  People(name,sex,age) {
    this.name = name;
    this.sex = sex;
    this.age = age;
}

People.prototype = {
    sayHello : function(){
        console.log(this.name+this.sex+this.age);
    }
}

//此时，People被视为构造函数，可以用new来实例化
module.exports = People;
```

#### 1.2.2.创建属性与函数文件
``` js
var msg = "你好";
function show() {
    console.log("啦啦啦");
}

exports.msg = msg;
exports.show = show;
```

#### 1.2.3.测试：test
test1文件夹:module模块入门
- 模块就是一些功能的封装，所以一些成熟的、经常使用的功能都有人封装成了模块，并且放到了社区中，供人免费下载。
- 这个伟大的社区叫做**npm**,也是一个工具的名字 node package management。读者可以在[npm官网](https://www.npmjs.com/)下载并使用相关模块。


**对应test.js**
- 因为模块文件夹为node_modules(默认文件夹),所以前面不需要./node_modules
- require("bar")相当于require("node_modules/bar/index.js")
- 有package.json时，require("bar")会根据node_modules/bar/package.json中main对应的js文件进行调用

``` js
// test.js
var test = require("./node_modules/test");

console.log(test.msg);
test.show();
```

**对应People.js**
- 因为模块文件夹为node_modules(默认文件夹),所以前面不需要./node_modules

``` js
var People = require("./node_modules/People");

var xiaoming = new People("小明","nan","12");

xiaoming.sayHello();
```

### 1.3.示例：ejs-moban
- 这里仅仅使用了ejs非常基础的用法，仅仅可以起到演示的作用，如果读者想深入了解ejs的更多知识，请前往[EJS官网](https://ejs.bootcss.com/)查看相关文档。

#### 1.3.1.ejs测试
``` js
var ejs = require("ejs");

//模板
var string = "好高兴啊,今天买了华为mate<%= a %>"
//数据
var data = {
    a:20
};

//数据绑定
var html = ejs.render(string,data);
//输出
console.log(html);
```

#### 1.3.2.使用ejs
- 首先创建els文件，在node.js文件里为该ejs绑定数据，然后render返回ejs静态文件即可。

**index.ejs**
``` ejs
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <h1>好高兴啊,今天买了华为mate<%= a %></h1>
    <ul>
        <% for (var i=0; i<news.length; i++){ %>
            <li><%= news[i] %></li>
        <% } %>
    </ul>
</body>

</html>
```
**数据绑定文件：demo.js**
``` js
var ejs = require("ejs");
var fs = require("fs");
var http = require("http")

var server = http.createServer(function (req,res) {
    fs.readFile("./views/index.ejs",function (err,data) {
        //绑定模板
        var template = data.toString();
        var dictionary = {
            a:20,
            news:["1班太牛逼啦","2班太牛逼啦","3班太牛逼啦","4班太牛逼啦","5班太牛逼啦","6班太牛逼啦"]
        };
        var html = ejs.render(template,dictionary);

        //显示
        res.writeHead(200,{"Content-Type":"text/html;charset=UTF8"});
        res.end(html)
    });
});

server.listen(3000,"127.0.0.1");
```

## 2.字符串处理
``` js
var http = require("http");
var querystring = require("querystring");


var server = http.createServer(function (req,res) {
    if(req.url == "/dopost" && req.method.toLowerCase() == "post"){
        var alldata = "";
        req.addListener("data",function (chunk) {
            alldata += chunk;
        });

        req.addListener("end",function () {
            var datastring = alldata.toString();
            var dataObj = querystring.parse(datastring);
            console.log(dataObj.name);
            console.log(dataObj.sex);
            console.log(dataObj.hobby)
            res.end();
        });
    }
});

server.listen(3000,"127.0.0.1");
```

## 3.文件上传
``` js
var http = require("http");
var querystring = require("querystring");
//引入工具类
var formidable = require('formidable');
var util= require("util");
var fs = require("fs");
var dateTime = require("date-time");
var path = require("path");


var server = http.createServer(function (req,res) {
    if(req.url == "/dopost" && req.method.toLowerCase() == "post"){

        // parse a file upload
        var form = new formidable.IncomingForm();
        //设置文件上传地址
        form.uploadDir = "./uploads";

        form.parse(req, function(err, fields, files) {
            if(err){
                throw err;
            }
            // console.log(fields);
            // console.log(files);
            //所有的文本域，单选框，都在fields存放
            //所有的文件，files

            //输出展开后的文件信息
            console.log(util.inspect({fields:fields,files:files}));

            //当前时间
            var time = dateTime();   //date-time模块函数          //目前这个时间有毒，有非法字符
            var ran = parseInt(Math.random() * 89999 + 10000);   //5位随机数
            var extname = path.extname(files.tupian.name);       //取文件后缀名

            //执行改名
            var oldpath = __dirname + "/" + files.tupian.path;
            //新的路径由三部分组成：时间戳，随机数，拓展名
            var newpath = __dirname + "/uploads/" + ran +extname;
            console.log(newpath);
            //改名
            fs.rename(oldpath,newpath,function (err) {
                if (err){
                    throw Error("改名失败");
                }
                res.writeHead(200, {'content-type': 'text/plain'});
                res.end("成功");
            });

        });

    }
});

server.listen(3000,"127.0.0.1");
```

## 4.初识express
- 这里仅仅简单展示了一个express的示例，目的是向读者展示Node.js可以通过express简单高效地管理数据，路由与页面，但其实express的功能比这强大得多，而且express在Node.js中有着很重要的地位。

``` js

var express  = require("express");

var bodyParser = require("body-parser");

var app = express();

//模板引擎
app.set("view engine","ejs");

app.get("/",function (req,res) {
    res.render("form");
})

app.use(bodyParser.urlencoded({ extended: false }));

app.post("/",function (req,res) {
    console.log(req.body);
})

app.listen(3000);
```

- 我们将会在下一节仔细讲述强大的express，学会使用express之后，我们便可以更加高效地编写Node.js程序。

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
