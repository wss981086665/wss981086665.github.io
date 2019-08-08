---
layout: post
title: Node.js学习笔记2-文件与静态资源
date: 2019-08-07 21:06:39
categories:
- Node.js
tags:
- Node.js
- 后端
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Node.js并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 4.文件
### 4.1.读取文件
- 对文件的操作主要是依赖fs模块，因此我们需要导入fs模块。下面的示例代码是对文件的简单读取操作，同时避免对图标进行访问，为请求加上请求头，以指定编码打开文件。

``` js
var http = require("http");
var fs = require("fs");

var server = http.createServer(function (req, res) {

  if (req.url == "/favicon.ico") {
    return; //避免访问图标
  }

  var userid = parseInt(Math.random * 89999) + 10000;
  //给用户加一个5位数的随机id
  console.log("欢迎" + userid);

  //设置http头部，状态码是200，文件类型是html
  res.writeHead(200, {
    "Content-Type": "text/html;charset=UTF8"
  });

  fs.readFile("./test/1.txt", {
    "charset": "utf-8"
  }, function (err, data) {
    if (err) {
      throw err;
    }
    console.log(userid + "文件读取完毕");
    res.end(data);
  });
});
server.listen(3000, "127.0.0.1");
```
### 4.2.文件目录操作
- 在下面的代码中，你将会看到与创建文件及创建文件夹相关的细节知识点、路径文件状态的检测——是否是文件夹等。
- 下面的程序会以数组的形式输出album目录下的所有文件夹名称。
``` js
var http = require("http");
var fs = require("fs");

var server = http.createServer(function (req, res) {

  if (req.url == "/favicon.ico") {
    return; //避免访问图标
  }

  //发现:文件夹必须要一层一层的创建
  // fs.mkdir("./album", function (err) {
  //   if (err) {
  //     throw err;
  //   }
  // });
  // fs.mkdir("./album/demo", function (err) {
  //   if (err) {
  //     throw err;
  //   }
  // });

  //检测该路径文件的状态
  // fs.stat("./album/demo", function (err, stats) {
  //   //检测这个路径是不是一个文件夹
  //   console.log("该路径文件是不是一个文件夹:"+stats.isDirectory());
  // })

  //只存储所有的文件夹=
  var wenjianjia = [];
  fs.readdir("./album/", function (err, files) {
    //files是个数组，包括./album文件夹中所有的文件及文件夹
    //迭代器就是强行把异步函数，变成同步的函数
    (function iterator(i) {
      //遍历结束
      if (i == files.length) {
        console.log(wenjianjia);
        return;
      }
      //stat检测状态
      fs.stat("./album/" + files[i], function (err, stats) {
        if (stats.isDirectory()) {
          wenjianjia.push(files[i]);
        }
        iterator(i + 1);
      })
    })(0);
  });

  res.end();

});
server.listen(3000, "127.0.0.1");
```

## 5.静态资源
- 创建static文件目录，static文件目录下包含几个html文件，现在我们通过将这些文件的名称作为请求URL从而实现对这些静态文件的访问。
- 由前面的例子我们可以知道，当图片需要在网页上显示时，也需要通过发送请求的方式才能显示在网页上，那么html文件也不例外，并且我们访问的文件可能会有很多种格式。我们可以将这些文件与对应的格式代码放入一个**json文件**中，然后请求时根据文件后缀在json文件中取得文件格式代码，这样我们只需要一个请求实现即可对所有文件请求进行处理。

**Node示例代码:**
``` js
var http = require("http");
var url = require("url");
var fs = require("fs");
var path = require("path");

var server = http.createServer(function(req, res) {
  //得到用户路径
  var pathname = url.parse(req.url).pathname;
  var fileUrl = "./"+path.normalize("./static/"+pathname);
  console.log(fileUrl);
  if (pathname == "/") {
    pathname = "index.html";
  }

  //拓展名
  var extname = path.extname(pathname);

  //真的读取这个文件
  fs.readFile("./static/" + pathname, function(err, data) {
    if (err) {
      //如果此文件不存在，就应该用404返回
      fs.readFile("./static/404.html", function(err, data) {
        res.writeHead(404, {
          "Content-Type": "text/html;charset=UTF8"
        });
        res.end(data);
      });
      return;
    }

    getMime(extname,function (mime) {
        res.writeHead(200, {"Content-Type": mime});
        res.end(data);
    });
  });
});
server.listen(3000, "127.0.0.1");
```

**getMime()函数**
``` js
function getMime(extname,callback) {
  //读取mime.json文件，得到JSON，根据extname key，返回对应的value
  //读取文件
  fs.readFile("./mime.json",function (err,data) {
     if(err){
       throw Error("找不到mime文件");
       return;
     }
     //转成JSON
     var mimeJSON = JSON.parse(data);
     var mime = mimeJSON[extname] || "text/plain";
     callback(mime);
  });
}
```

**JSON文件:**
``` json
{
  ".html": "text/html",
  ".jpg": "image/jpg",
  ".css": "text/css"
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