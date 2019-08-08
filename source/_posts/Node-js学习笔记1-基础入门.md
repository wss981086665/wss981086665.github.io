---
layout: post
title: Node.js学习笔记1-基础入门
date: 2019-08-07 21:06:28
categories:
- Node.js
tags:
- Node.js
- 后端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Node.js并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.初识Node
### 1.1.HelloWorld
``` js
//require表示引包，引包就是引用自己的一个特殊功能
var http = require("http");

var server = http.createServer(function(req, res) {
  res.writeHead(200, { "Content-type": "text/html;charset=UTF-8" });
  res.end("哈哈哈哈，这是我的第一个Node页面");
});

//运行服务器,监听3000端口(端口号可以任意改动)
server.listen(3000, "127.0.0.1");
```

### 1.2.使用http
``` js
//HTTP模块
var http = require("http");

http.createServer(function(req, res) {
    res.writeHead(200, {
      "Content-Type": "text/html;charset=UTF-8"
    });
    res.write("<h1>我是主标题</h1>");
    res.write("<h2>我是2标题</h2>");
    res.write("<h3>我是3标题</h3>");
    res.end("访问结束");
  })
  .listen(3000, "127.0.0.1");
```

### 1.3.解析URL
``` js
//URL模块
var http = require("http");
var url = require("url");

var server = http.createServer(function (req, res) {
    res.writeHead(200, {
        "Content-Type": "text/html;charset=UTF-8"
    });
    var pathname = url.parse(req.url).pathname;
    var query = url.parse(req.url, true).query;
    var id = query.id;

    console.log(pathname);
    console.log(query);
    console.log("id=" + id);
    res.end("访问结束");
});
server.listen(3000, "127.0.0.1");
```

### 1.4.根据请求URL进行判断执行
- 首先，列出这个例子是为了告诉读者有这种写法，这种根据URL进行判断执行的方法是非常低效的，不仅每个需要对每一个URL进行判断，还要对每一个选择的文件请求进行判断，试想一下，你的项目中需要显示几张图片，那么就需要分别对着几张图片进行URL判断并加载此图片。

``` js
//require表示引包，引包就是引用自己的一个特殊功能
var http = require("http");
var fs = require("fs");

//创建服务器，参数是一个回调函数，表示如果有请求进来，要做什么
var server = http.createServer(function (req, res) {
  if (req.url == "/yuan") {
    fs.readFile("./test.html", function (err, data) {
      //设置http头部，状态码是200，文件类型是html
      res.writeHead(200, {
        "Content-Type": "text/html;charset=UTF-8"
      });
      // res.end("哈哈哈哈，这是我的第一个Node页面");
      res.end(data);
    });
  } else if (req.url == "/bbb.css") { //html文件里涉及到访问文件
    fs.readFile("./css.css", function (err, data) {
      //设置http头部，状态码是200，文件类型是html
      res.writeHead(200, {
        "Content-Type": "text/css;charset=UTF-8"
      });
      // res.end("哈哈哈哈，这是我的第一个Node页面");
      res.end(data);
    });
  } else {
    res.writeHead(404, {
      "Content-Type": "text/html;charset=UTF-8"
    });
    res.end("找不到页面");
  }
});

//运行服务器,监听3000端口(端口号可以任意改动)
server.listen(3000, "127.0.0.1");
```
**其中的test.html与test2.html文件**
``` html
<!-- test.html文件省略了一些内容 -->
<head>
  <style>
    div {
      width: 200px;
      height: 200px;
      background-color: blue;
      border-radius: 50%;
    }
  </style>
  <link rel="stylesheet" type="text/css" href="bbb.css">
</head>
<body>
  <img src="abb.jpg" alt="">
  <div></div>
</body>

<!-- test2.html文件省略了一些内容 -->
<head>
  <style>
    div {
      width: 200px;
      height: 200px;
      background-color: yellow;
    }
  </style>
</head>
<body>
  <div></div>
</body>
```
**请求结果:**
![/yuan以及图片请求结果](https://upload-images.jianshu.io/upload_images/13687958-e3777b7ac1e90e50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2.表单信息提交测试
- 本小节主要是测试在网页中提交一些表单信息，并使用Node.js作为后端程序接收信息。

**Node代码:**
``` js
//信息提交测试
var http = require("http");
var url = require("url");

var server = http.createServer(function(req, res) {
  res.writeHead(200, {
    "Content-Type": "text/html;charset=UTF-8"
  });
  var queryObj = url.parse(req.url, true).query;
  var name = queryObj.name;
  var age = queryObj.age;
  var sex = queryObj.sex;

  res.write("姓名:" + name + " 年龄:" + age + " 性别:" + sex);

  console.log("姓名:" + name + " 年龄:" + age + " 性别:" + sex);
  res.end();
});
server.listen(3000, "127.0.0.1");
```
**前端body文件:**
``` html
<body>
  <form action="http://127.0.0.1:3000/" method="GET">
    姓名:<input name="name" type="text"></br>
    年龄:<input name="age" type="text"></br>
    性别:<input name="sex" type="radio" value="男"> 男
    <input name="sex" type="radio" value="女"> 女</br>
    <input type="submit">
  </form>
</body>
```

## 3.路由-router
- 下面给出的示例代码只能起到帮助你理解路由的概念，使用这样的路由方式进行生产并不是一个好的选择，但这并不代表这段代码毫无意义，请浏览并理解这段代码。

**路由-router示例:**
``` js
var http = require("http");

var server = http.createServer(function (req, res) {
  //得到url
  var userurl = req.url;
  res.writeHead(200, {
    "Content-Type": "text/html;charset=UTF-8"
  });

  //substr函数来判断此时的开头
  if (userurl.substr(0, 9) == "/student/") {
    var studentid = userurl.substr(9);
    if (/^\d{10}$/.test(studentid)) {
      res.end("您需要查询学生在信息，id为" + studentid);
    } else {
      res.end("学生学号位数不对")
    }
  } else if (userurl.substr(0, 9) == "/teacher/") {
    var teacherid = userurl.substr(9);
    if (/^\d{6}$/.test(teacherid)) {
      res.end("您需要查询老师在信息，id为" + teacherid);
    } else {
      res.end("老师学号位数不对")
    }
  }
});
server.listen(3000, "127.0.0.1");
```

**查询结果:**
![查询结果](https://upload-images.jianshu.io/upload_images/13687958-b294c9a16fab07b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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
