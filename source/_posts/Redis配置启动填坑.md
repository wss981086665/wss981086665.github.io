---
layout: post
title: Redis配置启动填坑
date: 2019-08-07 21:03:35
categories:
- Redis
tags:
- Redis
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Redis并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## Redis配置启动填坑
- 之所以要在这里单独写出一个文章记录Redis启动的问题，是因为作者在配置启动Redis的时候遇到了一些列问题，并且作者相信一定会有其他的读者会遇到相同的问题。

### 1.问题一：redis.conf文件在哪里，怎么操作
- 由于这里作者使用的是阿里云主机，因此**`redis.conf`**文件就在Redis的安装目录下，如果你使用的宝塔面板自动安装的Redis，那么Redis的安装目录在**`/www/server/redis`**下。请注意：不同主机各有差异。
- 作者不建议直接对此**`redis.conf`**文件进行操作，应该将此文件拷贝到另一个目录，对拷贝文件进行操作。

假如安装目录正是**/www/server/redis**，那么你可以这么复制：
``` linux
cp /www/server/redis /myredis/redis.conf
```
- 注意：首先你要先创建**`/myredis`**目录，创建文件的方式不需要獒述吧。

### 2.redis-server在哪里，如何使用
- 当作者刚刚安装好Redis的时，就直接使用**`redis-cli`**过了一把瘾，最后使用**`SHUDOWN`**终止了操作。然后，问题就来了，**`redis-cli`**命令再也无法正常启动。这是由于**`SHUDOWN`**终止了**redis服务**，正当我想通过**redis-server**启动Redis服务时，竟然有报错啦，这跟我看视屏学习的不一样啊，歇菜。
- 其实，**`redis-server`**等一系列指令在**`redis/src`**目录下，这应该是redis5.0之后做的更改，反正我是被坑了一把（我甚至还把Redis卸载重装了一下，很low）。
- 好，既然我们知道了**redis-server**以及**redis.conf**的位置以及作用，那么我们该如何启动呢，如下所示：

``` linux
redis-server /myredis/redis.conf
```

注意：这里应该使用**`quit`**退出**`redis-cli`**。

### 3.Redis如何后台启动
- 其实，当我第一次执行**redis-server /myredis/redis.conf**指令时，我也遇到了一点点问题，启动之后，出来的是一堆启动信息，还有Redis标志，总是就是告诉你启动成功了，但是我不知道怎么退出去使用啊，因此我直接**Ctrl + C**，结束了Redis服务，貌似刚才所做的事情全没了。
- 那么我们该怎么办呢，后台启动Redis服务不就行了吗，我们需要修改**redis.conf**文件中的配置信息。将**`daemonize no`**改为 **`daemonize yes`**保存退出