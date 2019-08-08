---
layout: post
title: Redis基础知识1-入门
date: 2019-08-07 21:02:57
categories:
- Redis
tags:
- Redis
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Redis并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## Redis基础知识
> **`Redis:`** **`RE`**mote **`DI`**ctionary **`S`**erver(远程字典服务器)

- **Redis是什么？**
1. 是完全开源免费的，用C语言编写，遵守BSD协议。
2. 是一个高性能的(key/value)分布式内存数据库，基于内存运行并支持持久化的**`NoSQL`**数据库，是当前**`最热门的NoSQL数据库之一`**，也被人们称为数据结构服务器。

- **Redis能干什么？**
**1**.内存存储和持久化：Redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务取最新N个数据的操作。如：可以将最新的10条评论的ID放在Redis的List集合里面。
**2**.模拟类似于HttpSession这种需要设定过期时间的功能。
**3**.发布、订阅消息系统。消息队列、排行榜、定时器、计数器。
**4**.发布、订阅消息系统。

- Redis去哪下载
[Redis中文网](http://www.redis.cn/)

- **Redis怎么玩？**
1.数据类型、基本操作和配置
2.持久化和复制，RDB/AOF
3.事务的控制
4.复制
5.......

- **最后让我们来看一看我们需要了解多少东西**
![Redis学习图谱](https://upload-images.jianshu.io/upload_images/13687958-6cac4ad1b84f5a5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 注：图片引用自掘金文章[Redis闲谈](https://juejin.im/post/5cce56cee51d453aa307c80e)，如有侵权，请联系博主删除。


## Redis使用
### 1.Redis安装
> 在此不介绍安装Redis相关知识，具体步骤请读者自行百度查找。(建议使用Linux环境)。博主在Redis基础教程系列中所使用的系统环境是ubantu18.04

### 2.相关知识
- Redis默认设有16个库
- Redis索引都是从0开始
- Redis默认端口是6379

### 3.基础操作
#### 通过命令行客户端访问Redis
``` Linux
redis-cli
```
#### 性能测试
``` linux
redis-benchmark
```
#### 切换库
``` linux
select x                # x是指要切换到的库的id
```
#### 查看库中key的数量
``` linux
dbsize
```
#### 查看库中所有的key-value
``` linux
keys *
```
#### 模糊查询
``` linux
# 查询'k1','k2','k3'
keys key?
```
#### 清空库中数据
``` linux
flushdb
```
## 4.Redis五大数据类型
- Redis键(key)
- Redis字符串(String)
- Redis列表(List)
- Redis集合(Set)
- Redis哈希(Hash)
- Redis有序集合Zset(sorted set)

### String(字符串)
> - String是Redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。
> - String类型是二进制安全的。意思是Redis的String可以包含任何数据。比如jpg图片或者序列化的对象。
> - String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M。

### Hash(哈希)
> - Redis hash是一个键值对集合。
> - Redis hash是一个String类型的field和value的映射表，hash特别适合用于存储对象。
> - 类似于Java里面的Map<String,Object>

### List(列表)
> - Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部(左边)或者尾部(右边)。它的底层实际是个链表

### Set(集合)
> - Redis的Set是String类型的无序集合。它是通过HashTable实现的。

### Zset(sorted set : 有序集合)
> - Redis Zset和Set一样也是String类型元素的集合，且不允许有重复的成员。
> - **不同的是每个元素都会关联一个double类型的分数**
> - Redis正是通过分数来为集合中的成员进行从小到达的排序。**Zset的成员是唯一的，但分数(score)却可以重复。**

## 5.配置文件
**常用配置参数:**
- 1.Redis默认不是以守护进程的方式运行，可以通过配置项修改，使用yes启用守线程：
**`daemonize no`**
- 2.当Redis以守护线程方式运行时，Redis默认会把pid写入**/var/run/redis.pid**文件，可以通过pidfile指定：
**`pidfile /var/run/redis.pid`**
- 3.指定Redis监听端口，默认端口为**6379**，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379是手机按键上MERZ对用的号码，而MERZ取自意大利歌女Alessia Merz的名字：
**`port 6379`**
- 4.绑定的主机地址
**`bind 127.0.0.1`**
- 5.当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能：
**`timeout 300`**
- 6.指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为varbose：
**`loglevel varbose`**
- 7.日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置日志记录方式为标准输出，则日志将会发送给**/dev/null**：
**`logfile stdout`**

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
