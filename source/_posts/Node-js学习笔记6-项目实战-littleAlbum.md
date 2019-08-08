---
layout: post
title: Node.js学习笔记6-项目实战-littleAlbum
date: 2019-08-07 21:07:26
categories:
- Node.js
tags:
- Node.js
- 后端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Node.js并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.项目简介
- 这个项目是我跟着视频学习Node.js时跟着老师做的小项目，现在拿出来与读者分享一下，正好练习一下前面学过的知识。
- 我们将要实现的是一个小小相册。项目描述：有三个相册，我们可以查看相册里的图片，并且可以往相册里面上传图片。
- 在项目中我们主要使用的是express模块以及ejs来完成的。
- 在项目中，我们使用了模块化管理，文件操作模块也放在了单独的文件夹中，路由也单独由一个文件管理并向外暴露接口。

**主页页面展示:**
![主页面](https://upload-images.jianshu.io/upload_images/13687958-ac437f7c6e0de4c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**相册详情页面展示:**
![相册详情页面展示](https://upload-images.jianshu.io/upload_images/13687958-472f3c77fc19422d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**访问错误页面:**
![访问错误页面](https://upload-images.jianshu.io/upload_images/13687958-64c2df991d332de2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**目录结构:**
![目录结构](https://upload-images.jianshu.io/upload_images/13687958-a859286aa13a39eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2.代码展示
### 2.1.app.js
- **app.js**是一个Node项目的核心文件，它主要是使用中间接对请求进行处理，并且将各请求所需要执行的功能分发给相应的文件进行执行。

**app.js**
``` js
var express = require("express");
var app = express();

//package.json中main默认是index.js
// "main" : "router.js" 设置使得app.js中的router引入室不需要再controller后面加上/router.js
var router = require("./controller");

//设置模板引擎
app.set("view engine","ejs");
//路由中间件
//静态页面
app.use(express.static("./public"));
app.use(express.static("./uploads"));
//首页
app.get("/",router.showIndex);   //get请求回味回调函数showIndex自动加上req、res参数
app.get("/:albumName",router.showAlbum);
app.get("/up",router.showUp);
app.post("/up",router.doPost);

//404最后的中间件
app.use(function (req,res) {
    res.render("err",{
        "baseurl" : req.pathname
    })
})

app.listen(3000);
```

### 2.2.view模块

**album.ejs**
``` ejs
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>小小相册</title>
    <!--<link href="/</%= baseurl /%/>/css/bootstrap.min.css" rel="stylesheet">-->
    <link href="/css/bootstrap.min.css" rel="stylesheet">
    <!--  /就表示根目录，没有必要传值得到根目录路径  -->

</head>
<body>

    <nav class="navbar navbar-default">
        <div class="container-fluid">
            <!-- Brand and toggle get grouped for better mobile display -->
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="#">小小相册</a>
            </div>

            <!-- Collect the nav links, forms, and other content for toggling -->
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav">
                    <li><a href="/"> 全部相册 <span class="sr-only">(current)</span></a></li>
                    <li><a href="/up">上传</a></li>
                </ul>
            </div><!-- /.navbar-collapse -->
        </div><!-- /.container-fluid -->
    </nav>

    <ol class="breadcrumb">
        <li><a href="/">全部相册</a></li>
        <li class="active"><%= albumname %></li>
    </ol>

    <div class="container">
        <div class="row">
            <% for(var i=0; i<images.length; i++) { %>
                <div class="col-xs-6 col-md-3">
                    <a href="#" class="thumbnail">
                        <img src="<%= images[i] %>" alt="...">
                    </a>
                </div>
            <% } %>
        </div>
    </div>

<script src="/js/jQuery.js"></script>
<script src="/js/bootstrap.min.js"></script>
</body>
</html>
```

**err.ejs**
``` ejs
<!--已省略部分代码-->

<nav class="navbar navbar-default">
    <div class="container-fluid">
        <!-- Brand and toggle get grouped for better mobile display -->
        <div class="navbar-header">
            <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">小小相册</a>
        </div>

        <!-- Collect the nav links, forms, and other content for toggling -->
        <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
            <ul class="nav navbar-nav">
                <li class="active"><a href="#"> 全部相册 <span class="sr-only">(current)</span></a></li>
                <li><a href="#">上传</a></li>
            </ul>
        </div><!-- /.navbar-collapse -->
    </div><!-- /.container-fluid -->
</nav>

<div class="container">
    <img src="/images/404.jpg">
</div>
```

**index.ejs**
``` ejs
<nav class="navbar navbar-default">
        <div class="container-fluid">
            <!-- Brand and toggle get grouped for better mobile display -->
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="#">小小相册</a>
            </div>

            <!-- Collect the nav links, forms, and other content for toggling -->
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav">
                    <li class="active"><a href="/"> 全部相册 <span class="sr-only">(current)</span></a></li>
                    <li><a href="/up">上传</a></li>
                </ul>
            </div><!-- /.navbar-collapse -->
        </div><!-- /.container-fluid -->
    </nav>

    <div class="container">
        <div class="row">
            <% for(var i=0; i<albums.length; i++) { %>
                <div class="col-xs-6 col-md-3">
                    <a href="<%= albums[i] %>" class="thumbnail">
                        <img src="images/file.jpg" alt="...">
                    </a>
                    <h4><%= albums[i] %></h4>
                </div>
            <% } %>
        </div>
    </div>
```

**up.ejs**
``` ejs
<nav class="navbar navbar-default">
        <div class="container-fluid">
            <!-- Brand and toggle get grouped for better mobile display -->
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a class="navbar-brand" href="#">小小相册</a>
            </div>

            <!-- Collect the nav links, forms, and other content for toggling -->
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav">
                    <li><a href="/"> 全部相册 <span class="sr-only">(current)</span></a></li>
                    <li><a href="/up">上传</a></li>
                </ul>
            </div><!-- /.navbar-collapse -->
        </div><!-- /.container-fluid -->
    </nav>


    <div class="container">
        <div class="row">
            <form style="width: 40%;" method="post" action="#" enctype="multipart/form-data">
                <div class="form-group">
                    <label for="exampleInputEmail1">选择文件夹</label>
                    <select class="form-control" name="wenjianjia">
                        <% for(var i=0; i<albums.length; i++) { %>
                            <option><%= albums[i] %></option>
                        <% } %>
                    </select>
                </div>
                <div class="form-group">
                    <label for="exampleInputFile">选择图片</label>
                    <input type="file" id="exampleInputFile" name="tupian">
                </div>
                <button type="submit" class="btn btn-default">上传</button>
            </form>
        </div>
    </div>
```

### 2.2.路由模块
**package.json**
``` json
{
  "main" : "router.js"
}
```

**router.js**
- 其实这个路由文件中存储的是没一个路由执行所需要调用的方法，这些方法以暴露接口的方式供外部调用，每个方法执行特定的功能。
- router.js中调用了以下几个模块--formidable、util、fs、path、date-time模块，同时引用了部分其他文件。

``` js
var file = require("../modejs/file.js");
var formidable = require('formidable');
var util= require("util");
var fs = require("fs");
var path = require("path");
var dateTime = require("date-time");

//首页
exports.showIndex = function (req,res) {

    //传统思维，不是nodejs异步的思维方式
    //这种方法会造成还没有获取到数据就把值传过去了,异步执行
    // res.render("index",{
    //     "albums" : file.getAllAlbums()
    // });

    //这就是Node.js的编程思维,所有的东西都是异步的
    //所以内层函数不是return回来东西，而是调用高层函数提供的回调函数，把数据当做回调函数的参数使用
    file.getAllAlbums(function (err,allAlbums) {
        if(err){
            res.send(err);
            return;
        }
        res.render("index",{             //向静态页面(index.ejs)传输数据并显示index.ejs页面
            "albums" : allAlbums
        })
    })
}

exports.showAlbum = function (req,res,next) {
    //遍历相册中的所有图片
    var albumName = req.params.albumName;
    //具体业务交给model
    file.getAllImagesByAlbumName(albumName,function (err,imagesArray) {
        if(err){
            // res.send(err);
            next();
            return;
        }
        res.render("album",{
            "albumname" : albumName,
            "images" : imagesArray
        })
    })
}

//显示上传
exports.showUp = function (req,res) {
    file.getAllAlbums(function (err,allAblums) {
        if(err){
            res.send(err);
            return;
        }
        res.render("up",{
            "albums" : allAblums
        })
    })
}

exports.doPost = function (req,res) {
    var form = new formidable.IncomingForm();

    form.uploadDir = path.normalize(__dirname + "/../tempup");

    form.parse(req, function(err, fields, files,next) {
        console.log(fields);
        console.log(files);
        //改名
        if(err){
            next();
            return;
        }

        // //判断尺寸
        // var size = parseInt(files.tupian.size);
        // if(size > 2048){
        //     res.send("图片大小应该小于2M");
        //     //删除图片
        //     fs.unlink(files.tupian.path,function (err) {
        //         if (err){
        //             res.send(err);
        //             return;
        //         }
        //     });
        //     return;
        // }

        var time = dateTime();   //date-time模块函数          //目前这个时间有毒，有非法字符
        var ran = parseInt(Math.random() * 89999 + 10000);   //5位随机数
        var extname = path.extname(files.tupian.name);       //取文件后缀名

        var wenjianjia = fields.wenjianjia;
        var oldpath = files.tupian.path;
        var newpath = path.normalize(__dirname + "/../uploads/" + wenjianjia + "/" + ran + extname);
        fs.rename(oldpath,newpath,function (err) {
            if (err){
                throw Error("改名失败");
                return;
            }
            res.end("成功");
        });
    });
}
```

### 2.3.文件操作模块
- 文件操作模块实现了对文件进行操作的方法，以供router.js等文件调用。

``` js
var fs = require("fs");

//这个函数的callback中含有两个参数，一个是err
//另一个是存放所有文件夹名称的array
exports.getAllAlbums = function (callback) {
    fs.readdir("./uploads",function (err,files) {
        var allAlbums = [];

        if(err){
            callback("没有找到uploads文件夹",null)
        }

        (function iterator(i) {
            if(i == files.length){
                //遍历结束
                console.log(allAlbums);
                //我们现在集中精力，找到所有文件夹
                callback(null,allAlbums);
                return;
            }
            fs.stat("./uploads/" + files[i],function (err,stats) {
                if(err){
                    callback("找不到文件" + files[i],null);
                    return;
                }
                if(stats.isDirectory()){
                    allAlbums.push(files[i]);
                }
                iterator(i + 1);
            })
        })(0)
    })
}

//通过文件名得到所有图片
exports.getAllImagesByAlbumName = function (albumName,callback) {
    fs.readdir("./uploads/" + albumName,function (err,images) {
        var allImages = [];

        if(err){
            callback("没有找到uploads文件夹",null);
            return;
        }

        (function iterator(i) {
            if(i == images.length){
                //遍历结束
                console.log(allImages);
                //我们现在集中精力，找到所有文件夹
                callback(null,allImages);
                return;
            }
            fs.stat("./uploads/" + albumName + "/" + images[i],function (err,stats) {
                if(err){
                    callback("找不到文件" + images[i],null);
                    return;
                }
                if(stats.isFile()){
                    allImages.push(images[i]);
                }
                iterator(i + 1);
            })
        })(0)
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