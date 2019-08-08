---
layout: post
title: Node.js学习笔记5-Express与中间件
date: 2019-08-07 21:07:16
categories:
- Node.js
tags:
- Node.js
- 后端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Node.js并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.Express框架
### 1.1.Express入门
- 当我们使用了Express模块之后，高效就不在是梦想。
- 下面的示例仅仅是使用了**app.get()**进行解析，很简单的就实现了前文用路由实现的的繁琐的逻辑。代码并不难看懂，并且这里只是为了展示，如果想深入了解express，读者可以去[Express官网](http://www.expressjs.com.cn/4x/api.html)查看相关文档。

``` js
//express路由基础操作

var express  = require("express");

var app = express();

app.get("/",function (req,res) {
    res.send("你好");
})

app.get("/haha",function (req,res) {
    res.send("这是haha页面,哈哈哈哈哈")
})

app.get(/^\/student\/([\d]{10})$/,function (req,res) {
    res.send("学生信息:学号:" + req.params[0]);
})

app.get("/teacher/:gonghao",function (req,res) {
    res.send("老师的工号是:" + req.params.gonghao);  //获取参数两种方法1
});

1.2
app.get("/student/:id",function (req,res) {
    var id = req.params["id"];      //获取参数两种方法2
    var reg = /^[\d]{6}$/;        //正则表达式的使用
    if(reg.test(id)){
        res.send(id);
    }else{
        res.send("请检查格式");
    }
});

app.listen(3000);
```

### 1.2.express访问静态文件
- 由前文我们可以知道，当使用原生Node.js操作静态文件时，我们需要对每一个文件都进行访问解析，如果不这么做，就无法访问得到静态文件。
- 在express中，这种操作变得极为简单，正如下面示例所示。

**访问静态文件**
``` js
//express静态文件访问基础操作

var express  = require("express");

var app = express();

app.use(express.static("./public"));  //即./public/index.html

app.get("/haha",function (req,res) {
    res.send("你好啊-哈哈页面");
})

app.listen(3000);
```

### 1.3.搭配使用ejs
#### 1.3.1.示例一：绑定数组数据

**haha.ejs文件:**
``` ejs
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link rel="stylesheet" type="text/css" media="screen" href="main.css" />
    <script src="main.js"></script>
</head>
<body>
    <h1>你好啊</h1>
    <% for(var i=0; i<news.length; i++) {%>
        <li><%= news[i] %></li>
    <% } %>
</body>
</html>
```

**demo3(render).js文件:**
``` js
//express模板引擎基础操作

var express  = require("express");

var app = express();

//app.set("views","a");  //将默认文件夹(views)更改为文件夹a

//设置模板引擎ejs
app.set("view engine","ejs");

app.get("/",function (req,res) {
    res.render("haha",{
        "news": ["11111","222222","333333"]
    });
});

app.listen(3000);
```

#### 1.3.2.示例二：动态绑定数据
**form4.ejs文件:**
``` ejs
<!--搭配demo.js练习-->

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link rel="stylesheet" type="text/css" media="screen" href="main.css" />
    <script src="main.js"></script>
</head>
<body>
<h1>你好啊</h1>
    <form action="#" method="post">
        <input type="text" name="name">

        <input type="submit">
    </form>
</body>
</html>
```

**demo4.js文件:**
``` js
//express模板引擎基础操作
//搭配form.ejs练习

var express  = require("express");

var app = express();

//设置模板引擎ejs
app.set("view engine","ejs");

app.get("/",function (req,res) {
    res.render("form");
});

app.post("/",function (req,res) {
    //将数据添加至数据库
    res.send("成功");
})

app.listen(3000);
```

## 2.中间件
### 2.1.认识中间件
- get、post这些东西，就是中间件，中间件讲究顺序，匹配到第一个之后，就不会往后匹配了。使用next函数才会继续匹配

``` js
var express  = require("express");

var app = express();

app.get("/",function (req,res,next) {
    console.log("1");
    next();
});

app.get("/",function (req,res) {
    console.log("2");
});

app.listen(3000);
```

### 2.2.中间件的使用
``` js
var express  = require("express");
var fs = require("fs");

var app = express();

//app.use也是一个中间件
//app.use中的指令可以匹配多个
//如admin,  admin/asdf,  admin/asdf/ghjk都可以匹配上(任何以admin开头的请求)
//依然，如果app.use中没有使用next函数的话，程序只会执行匹配第一个匹配到的路径，匹配过后不会继续行
//当不写路径的时候，实际就相当于 "/" ,就是所有网址;
// 具体的往上写，抽象的往下写
app.use("/",function (req,res,next) {    // 1
    console.log(new Date());
    next();
})

// 1 与 2 用法相同
app.use(haha);                           // 2
function haha(req,res,next) {
    var filepath = req.originalUrl;
    //根据当前网址 读取public文件夹中的文件
    //如果有这个文件，那么渲染这个文件
    //如果没有这个文件，那么next();
    // console.log(new Date());
    fs.readFile("./public/" + filepath,function (err,data) {
        if(err){
            //文件不存在
            next();
            return;
        }
        res.send(data.toString());
    });
}

app.use("/admin",function (req,res) {
    res.write(req.originalUrl + "\n");
    res.write(req.path + "\n");
    res.write(req.baseUrl + "\n");
    res.end("你好");
})

app.listen(3000);
```

### 2.3.渲染静态资源
``` js
var express  = require("express");

var app = express();

//中间件 use 一般不使用  静态服务的时候可以使用
//静态服务
app.use("/jingtai",express.static("./public"));

app.get("/images",function (req,res) {
    res.send("哈哈");
});

app.use(function (req,res) {
    res.status(404).send("没有这个页面!")
})

app.listen(3000);
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
