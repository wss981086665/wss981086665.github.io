---
layout: post
title: Node.js学习笔记-https与跨域问题
date: 2019-08-07 21:07:35
categories:
- Node.js
tags:
- Node.js
- 后端
---
## 一、Node.js知识点
### 1.Node.js配置https
``` js
var express = require(“express”);
var app = express();       //使用nodejs自带的http、https模块
var https = require('https');
var http = require('http');
var fs = require('fs');    //用于读入证书文件
var path = require("path");
//根据项目的路径导入生成的证书文件 
var privateKey  = fs.readFileSync(path.join(__dirname, '/public/215028913200048.key'), 'utf8');
var certificate = fs.readFileSync(path.join(__dirname, '/public/215028913200048.pem'), 'utf8');
var credentials = {key: privateKey, cert: certificate};
//创建http与HTTPS服务器
var httpServer = http.createServer(app,function () {
    console.log('HTTP Server is running on: http://localhost:%s', PORT);
});
httpServer.listen(3000);
//创建http与HTTPS服务器
var httpsServer = https.createServer(credentials, app,function () {
    console.log('HTTPS Server is running on: https://localhost:%s', SSLPORT); });  //可以根据请求判断是http还是https 
    app.get('/', function (req, res) {
        if(req.protocol === 'https') {
            res.status(200).send('This is https visit!');
        } else {
            res.status(200).send('This is http visit!');
    }
});
httpsServer.listen(3001);
```
### 2.Node.js解决跨域问题
``` js
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By",' 3.2.1');
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});
```

## 3.ajax访问
- 一般情况下，使用微信小程序或Vue对后端进行访问时，都是需要https访问才可以，此时，我们只需要对Node程序进行https配置并且进行跨域处理即可。当然，配置方法上面已经给出。

**示例代码:**
``` js
var express = require("express");

var app = express();  //使用nodejs自带的http、https模块
var https = require('https');
var fs = require('fs');
var path = require("path");

//根据项目的路径导入生成的证书文件 
var privateKey  = fs.readFileSync(path.join(__dirname, '/public/215028913200048.key'), 'utf8');
var certificate = fs.readFileSync(path.join(__dirname, '/public/215028913200048.pem'), 'utf8');
var credentials = {key: privateKey, cert: certificate};

//创建http与HTTPS服务器
var httpsServer = https.createServer(credentials, app, function () {
    console.log('HTTPS Server is running on: https://localhost:%s');
});

//设置跨域访问
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    res.header("X-Powered-By",' 3.2.1');
    res.header("Content-Type", "application/json;charset=utf-8");
    next();
});

app.get('/',function (req, res) {
    res.status(200).send('This is https visit!');
    console.log("lalala");
});
app.get('/news', function (req, res) {
    console.log("历史性突破诞生啦");
})
httpsServer.listen(3000);
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
