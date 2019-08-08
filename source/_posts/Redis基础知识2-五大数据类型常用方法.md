---
layout: post
title: Redis基础知识2-五大数据类型常用方法
date: 2019-08-07 21:03:08
categories:
- Redis
tags:
- Redis
- 数据库
---
- **注意:** 撰写本文目的主要是**为了给自己做一个备忘录**，如果你学过Redis并且希望从本文中找到一些忘记的知识点，那么你可以阅读本文章。由于文章内讲解并不是很多，因此此文章并不适合小白入门使用。

## 1.Redis五大常用数据类型常用方法
### 键(key)常用方法
``` linux
# 查询库中所有数据
keys *

# 判断某个key是否存在
exists key的名字

# 将某个数据移除到另一个库中，移除过后当前库中就没有此数据了
move key db

# 为给定的key设置过期时间
expire key 秒钟

# 查看还有多少秒过期，-1表示永不过期，-2表示已过期
ttl key

# 查看一个key对应的数据是什么类型
type key
```
### 字符串(String)常用方法
``` linux
# 删除一个键
del key

# 为已有key对应的value添加字符串
append k1 '123456'

# 获取字符串长度
strlen key

# 数字自增1    #一定要是数字才能进行
incr key

# 数字自减1
decr key

# 数字添加指定数值
incrby key num

# 数字减去指定数值
decrby key num

# 获取指定区间范围内的值
# 从0到-1表示全部
getrange key 0 -1      #获取全部
getrange key 0 2       #0<=range<=3

# setex(set with expire)    设置值的同时指定有效时间
# setnx(set if not exist)   如果不存在此key，才为key设置新value
setex key time value
setnx key value 

# 同时为多个key设置value
mset k1 v1 k2 v2 k3 v3         

# 同时获取多个key的value
mget k1 k2 k3

# 如果key都不存在，才进行添加操作
msetnx k3 v3 k4 v4   
```
### 列表(List)常用方法
``` linux
# 左侧连续插入数据
lpush list01 1 2 3 4 5

# 读出列表中所有数据
lrange list01 0 -1

# 弹出列表左侧数据
lpop list01

# 按照索引下标获得元素(从左到右，下标从0开始)
lindex list02 2

# 获得列表的长度
llen list01

# 删除N个value
lrem list02 2 3     # 从列表list02中删除2个3

# 截取指定范围的值后再赋值给自身
ltrim list01 0 4     # 0：开始index，4：结束index

# 将list01最右侧的值弹出并添加到list02最左侧
rpoplpush list01 list02

# 为指定列表左侧开始第index个值设置新值
lset key index value

# 在指定值之前/之后插入value。左侧开始查找
linsert key before/after 值1 值2
```
**List注意是事项：**
- 它是一个字符串链表，left、right都可以插入添加。
- 如果键不存在，创建新的列表；如果键已存在，增加内容。
- 如果值全部移除，对应的键也就消失了。
- 链表的操作无论是头和尾效率都是极高的，但假如是对中间元素进行操作，效率就相对较低。

### 集合(Set)常用方法

``` linux
# 向指定列表中添加元素，且自动去重sadd set01
sadd set01 1 1 2 2 3 3

# 查看set集合中所有元素
smembers set01

# 判断集合中是否包含指定元素
sismember set01 value

# 获取集合里面的元素个数
scard set01

# 删除集合中的元素
srem set01 value

# 从集合中随即抽取3个数
srandmember set01 3

# 将key1中的某个值转移到key2中
smove key1 key2 key1中的某个值

# 数学集合2
# 求差集：sdiff  ：在第一个set里面但不在后面任何一个set里面的项
sdiff set01 set02

# 求交集：sinter
sinter set01 set02

# 求并集：sunion
sunion set01 set02
```

### 哈希(Hash)常用方法
- KV模式不变，但V是一个键值对
- **`重要：hset/hget/hmset/hmget/hgetall/hdel`**

``` linux
# 向指定key中添加KV
hset user id 11
hset user name z3

# 获取指定key中key对应的元素
hget user id
hget user name

# 一次向指定key中插入多个键值对
hmset customer id 11 name li4 age 26

# 一次查询指定key中多个键的值
hmget customer id name age

# 获取指定key中所有KV
hgetall customer

# 删除指定key中的一个key
hdel user name

# 获取指定key中KV的数量
hlen user

# 判断指定key中是否存在指定KV
hexists id

# 获取指定key中所有的K
hkeys customer

# 获取指定key中所有的V
hvals customer

# 指定key中的指定K加上指定的值
hincrby customer age 2

# 指定key中的指定K加上指定的浮点值
hincrbyfloat customer score 0.5

# 如果不存在才添加元素
hsetnx customer age 26
```

### Zset(集合)常用方法
- 在set基础上，加一个score值。
- 之前set是**k1 v1 v2 v3**。
- 现在zset是k1 score1 v1 score2 v2。相当于KV是一个键值对。

``` linux
# 向zset中添加元素
zadd zset01 60 v1 70 v2 80 v3

# 列出所有的value
zrange zset01 0 -1

# 列出所有的value和score
zrange zset01 0 -1 withscores

# 查询指定范围score1<=x<=score2内的value
zrangebyscore zset01 60 90

# 查询指定范围score1<=x<score2内的value
zrangebyscore zset01 60 (90

# 查询指定范围score1<x<score2内的value
zrangebyscore zset01 (60 (90

# 查询指定范围score1<=x<=score2内的value,并从指定位置截取指定个数的元素。
zrangebyscore zset01 60 90 limit 2 2

# 删除指定集合中的指定元素
zrem zset01 v5

# 统计指定集合中V的个数
zcard zset01

# 统计指定范围内V的个数
zcount zset01 60 80

# 获取V的在集合中的下标
zrank zset01 v4

# 获取V在集合中对应的score
zscore zset01 v4

# 逆序获得V在集合中的下标值
zrevrank zset01 v4

# 逆序打印集合中的v和score
zrevrange zset 0 -1

# 逆序获得指定范围内的V，注意：90 与 60 也要反转
zrevrangebyscore zset01 90 60
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