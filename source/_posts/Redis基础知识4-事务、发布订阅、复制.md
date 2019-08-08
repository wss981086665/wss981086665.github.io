---
layout: post
title: Redis基础知识4-事务、发布订阅、复制
date: 2019-08-07 21:03:26
categories:
- Redis
tags:
- Redis
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Redis并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.事务
### 1.1事务简介
**是什么：**
- 可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会序列化，**`按顺序地串行化执行而不会被其他命令插入，不允许加塞`**。

**能干嘛：**
一个队列中，一次性、顺序性、排他性地执行一系列命令。

**怎么玩：**
- 常用命令
- Case1：正常执行
- Case2：放弃事务
- Case3：全体连坐
如果一个指令在加入事务时发生错误，则事务块中所有的指令失效。
- Case4：冤头债主
如果一个指令可以加入到事务中，但逻辑上是错误的，则在执行事务块中的语句时此语句会报错，但不会影响到其他的语句。
- Case5：watch监控

### 1.2.Redis事务命令
![Redis事务命令](https://upload-images.jianshu.io/upload_images/13687958-df14e5afecbde486.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**使用示例：**
``` linux
> MULTI
> set k1 v1
> set k2 v2
> get k2
> set k3 v3

> EXEC
输出结果：
OK
OK
"v2"
OK
```

### 1.3.watch监控
- 监视一个（或多个）key，如果在事务执行之前这个（或这些）key被其他命令所改动，那么事务将被打断。
**监控**
``` linux
watch key
```
**取消监控**
``` linux
unwatch
```
- 一旦执行了exec操作，之前加的监控锁都会被取消掉。

**小结：**
- 通过WATCH命令在事务执行之前监控了多个Keys，倘若在WATCH之后有任何Key的值发生了变化，EXEC命令执行的事务都将被抛弃，同时返回Nullmulti-bulk应答以通知调用者事务执行失败。

### 1.4.事务3阶段、3特性
**3阶段**
![事务3阶段](https://upload-images.jianshu.io/upload_images/13687958-0129146a1b74f9fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3特性**
![事务3特性](https://upload-images.jianshu.io/upload_images/13687958-6519dbb2738ff512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2.发布与订阅
### 2.1.是什么
- 进程间的一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息。
- 订阅/发布消息图:

![订阅/发布消息图](https://upload-images.jianshu.io/upload_images/13687958-105696aa79d32345.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2.命令
![命令](https://upload-images.jianshu.io/upload_images/13687958-0f4e15b95e1f5cd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.3.案例
![案例](https://upload-images.jianshu.io/upload_images/13687958-869ff17305ce8341.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.主从复制
### 3.1.复制
**是什么：**
- 行话：也就是我们所说的主从复制，主机数据更新后根据配置和策略，自动同步到备机的**mastre/slaver**机制，**Master**以写为主，**Slave**以读为主。

**能干嘛：**
- 读写分离
- 容灾恢复

**怎么玩：**
- 1.配从（库）不配主（库）
- 2.从库配置：
(1).连接主库：**slaveof 主库IP 主库端口**
每次与master断开之后，都需要重新连接，除非你配置进redis.conf文件
(2).Info replication：查看当前状态（主机或是备机以及其他状态）
- 3.修改配置文件细节操作

![修改配置文件细节操作](https://upload-images.jianshu.io/upload_images/13687958-3ececf51bfc524b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 4.常用3招
(1).**一主二仆**：一台主机，两台备机
(2).**薪火相传**：
![薪火相传](https://upload-images.jianshu.io/upload_images/13687958-1d3e734383ad0aa2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
(3).**反客为主**:
将从机设置为主机：**`SLAVEOF no one`**，使当前数据库停止与其他数据库同步，转成主数据库。

### 3.2.复制原理
![复制原理](https://upload-images.jianshu.io/upload_images/13687958-85e57a8c5ee05179.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.3.哨兵模式(sentinel)
**是什么：**
- 哨兵模式就是反客为主的自动版，能够后台监控主机是否故障，如果故障了就根据投票数自动将从库转为主库

**使用步骤：**
- 自定义的/myredis目录下新建**`sentinel.conf`**文件，名字绝对不能错。
- 配置哨兵，填写内容

``` linux
sentinel monitor 被监控数据库名字（自己起个名字） 127.0.0.1 6379 1
# 上面最后一个数字1，表示主机挂掉后slave投票看让谁接替成为主机，得票数多的成为主机
```
- 启动哨兵
``` linux
redis-sentinel /myredis/sentinel.conf
# 上述目录依照各自的实际情况配置，可能目录不同
```

### 3.4.复制的缺点
- 由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以Master同步到Slave机器有一定的延时，当系统很繁忙的时候，延时问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

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