---
layout: post
title: Redis基础知识3-持久化
date: 2019-08-07 21:03:17
categories:
- Redis
tags:
- Redis
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Redis并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.Redis简介
- **官网介绍：**
在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的**Snapshot快照**，它恢复时是将快照文件直接读到内存里。
-  **Redis是什么：**
Redis会单独创建（**`fork`**）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何**`IO操作`**的，这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是很敏感，那**`RDB方式`**要比**`AOP方式`**更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。
- **Fork：**
Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。

## 2.RDB(Redis DataBase)
**RDB存储：**
- RDBRedis会将数据存储在**dump.rdb**文件中。
- 如果使用**shutdown**或者系统突然崩溃或关机使得Redis突然停止工作时，Redis会将此时表中的数据保存至**dump.rdb**文件中，执行一次save操作。
- 当满足save条件时（规定时间内操作了指定次数：插入、删除、更新，不包括get），Redis会将表中的数据保存至dump.rdb文件中，也会执行save操作。save操作执行条件如下（**`save 秒钟 写操作次数`**）：
![isave执行条件](https://upload-images.jianshu.io/upload_images/13687958-8f54fbe56f6d1767.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Redis.conf中Snapshot相关配置:**
![Redis.conf中Snapshot相关配置](https://upload-images.jianshu.io/upload_images/13687958-3798689713bbd6d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 直接输入save可以立即执行备份。

**禁用：**
- 如果想禁用RDB持久化策略，只要不设置任何save指令，或者给save传入一个空字符串也可以

**Stop-writes-on-bgsave-error**
![Stop-writes-on-bgsave-error](https://upload-images.jianshu.io/upload_images/13687958-782952eccd845ec7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**rdbcompression压缩**
![rdbcompression压缩](https://upload-images.jianshu.io/upload_images/13687958-31c568601666d24f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**如何触发RDB快照：**
- 配置文件中默认的快照配置。
冷拷贝直接使用，可以**cp dump.rdb dump_new.rdb**
- 命令**save**或是**bgsave**。
1.**Save**：save时只管保存，其他不管，全部阻塞。
2.**BGSAVE**：Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。可以通过**lastsave命令**获取最后一次成功执行快照的时间。
- 执行**flushall**命令，也会产生dump.rdb文件，但里面是空的，无意义。

**如何恢复：**
- 将备份文件（dump.rdb）移动到redis安装目录并启动服务即可。
- CONFIG GET dir获取目录。

**优势：**
- 适合大规模的数据恢复。
- 对数据完整性和一致性要求不高。

**劣势：**
- 在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会失去最后一次快照后的所有修改。
- Fork的时候，内存中的数据被克隆了一份，大致2倍的碰着性需要考虑。

**如何停止：**
- 动态所有停止RDB保存规则的方法：**redis-cli config set save ""**

## 3.AOF(Append Only File)
### 3.1.是什么
- **以日志的形式来记录每个文件写操作**，将Redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将指令从前到后执行一次以完成数据的恢复工作。

### 3.2.AOF配置文件
![AOF配置文件](https://upload-images.jianshu.io/upload_images/13687958-a993a49837644ef3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.3.注意事项
- 自动生成的aof配置文件最后会有**FLUSHALL**指令，即加载完所有的数据恢复工作之后，会执行**FLUSHALL**清空指令。
- 若AOF配置中包含非法语句，则Redis-server会启动失败。此时，可以使用**redis-check-aof --fix 文件名称**来排查文件中的错误。

### 3.4.配置位置
- **appendonly no：**默认是**no**，**yes**就打开**aof持久化**。

### 3.5.APPEND ONLY MODE追加
- **appendonly：**
- **appendfilename：**
- **Appendfsync：**
1.Always：同步持久化 每次发生数据变更会被立即记录到磁盘 性能较差且数据完整性比较好。
2.Everysec：出厂默认推荐，异步操作，每秒纪录 如果一秒内宕机，有数据丢失。
3.No**
- **No-appendfsync-on-rewrite ：**重写时是否可以运用Appendfsync，用默认no即可，保证数据安全性。
- **Auto-aof-rewrite-min-size：**设置重写的基准值
- **Auto-aof-rewrite-percentage：**设置重写的基准值

### 3.6.AOF启动/修复/恢复
- **正常恢复：**
1.启动：修改默认的**appendonly no**.，改为**yes**。
2.将所有数据的aof文件复制一份保存到对应目录（config get dir）
3.恢复：重启redis然后重新加载
- **异常恢复：**
1.启动：设置Yes
2.备份被写坏的AOF文件
3.Redis-check-aof --fix进行修复
4.重启redis然后重新加载

### 3.7.Rewrite
**是什么：**
AOF采用文件追加方式，文件会越来越大，为避免出现此情况，新增了重写机制，当AOF文件的大小超过所有设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集，可以使用命令**bgrewriteaof**

**重写原理**
- AOF文件持续增长而过大时，会**fork**出一条新进程来将文件重写（也是先写临时文件最后再rename），遍历新进程的内存中数据，每条记录有一条的Set语句，重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。

**触发机制**
- Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发。

### 3.8.优势
- 每秒同步：appendfsync always  同步持久化 每次发生数据变更会被立即记录到磁盘 性能较差且数据完整性比较好。
- 每修改同步：appendfsync everysec  出厂默认推荐，异步操作，每秒纪录 如果一秒内宕机，有数据丢失。
- 不同步：appendfsync no  从不同步

### 3.9.劣势
- 相同数据集的数据而言aof文件要远大于rdb文件，恢复速度慢于rdb
- Aof运行效率要慢于rdn，每秒同步策略效果较好，不同步效率和rdb相同

### 3.10.总结
![总结](https://upload-images.jianshu.io/upload_images/13687958-c9a8b130f1b006e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**性能建议：**
![性能建议](https://upload-images.jianshu.io/upload_images/13687958-7e8e8874ce84c99c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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