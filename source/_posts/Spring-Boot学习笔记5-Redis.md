---
title: Spring Boot学习笔记5(Redis)
date: 2019-05-04 10:19:25
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
- Redis
---
# 五、Redis
> - 开发中常用的缓存中间件: `resdis`、`memcached`、`chcache`
> - Spring Boot默认的缓存配置类为 `SimpleCacheConfiguration`
> - Redis是一个开源的(BSD许可)的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件，这里我们使用的是它的缓存中间件的功能
## 5.1.Redis常用指令
### 5.1.1.基本操作
Redis或者Spring Boot中的Key,通常都包含逻辑上的命名空间,用符号 ”:” 分开,比如spring:session:xxxxxx
``` java
> *set: 可以添加/覆盖一个字符串或数字类型
        例: set platform:info “simple infomation”
> *get: 获取对应key的值
        例: get platform:info
> *mget: 获取多个key的值
         例: mget platform:version platform:info
> *DECR/INCR: 数字类型数据自减和自增
> *DECRBY/INCRBY: 数字类型数据减去/增加某个指定的整数
> *INCRBYFLOAT: 数字增加一个浮点数,负数表示减去
```
注:以上操作都具有原子性
``` java
> *keys: 查询Redis中的key
        例: platform:* :表示所有patform:开的key
             *: 表示查询所有的key
> *exists: 判断Key是否存在
        例: exists platform:info                 //返回1表示存在,返回0表示不存在
> *del: 删除Key-value
         例: del platform:info                  //返回1表示删除成功,返回0表示失败
> *expire: 指定多少秒后Key-value自动删除
         例: expire platform 10
> *ttl: 查看Key的剩余存活时间
         例: ttl platform
```
### 5.1.2.Redis List
``` java
*rpush: 可以将多个值放入list尾部,也可以理解为放入右侧
          rpush platform:history “2012-1-1”  “2012-3-5”
*lpush: 与rpush相反
          lpush platform:history “2011-10-22”
*lrange: 从左到右显示指定范围的序列
          lrarge platform:history 0 2
*rpop: 从列表尾部取出一个元素
*lpop: 取出列表的头一个元素
*llen: 返回List的长度
*blpop或者brpop: 在List为空的时候处于等待状态,知道列表有元素,或者指定的时间到期为止
```
### 5.1.3.Redis Hash
``` java
*hset key field value: 给指定的Key设置一个字段值,如果值已经存在,则覆盖,返回0表示失败,返回1表示成功
         例: hset session:1xac name xiandafu
*hget key field: 获取指定Key的field字段的值,如果不存在返回nil
*hexists key field: 判断指定的Key的field字段是否存在,返回1存在,0不存在
*hkeys key: 返回指定Key所指定的hash所有的字段名
*hgetall key: 返回所有的字段名和字段值
*hdel key field [field]: 删除多个字段
*hincby key field value: 对Key指定的Hash数据中的field的值增加整型value
          例: hset website access 0
               hincrby website access 1         //此时数据变为1
*hincbyfloat key field value: 对Key指定Hash数据中的field的值进行增加浮点value
```
### 5.1.4.Set
``` java
*sad key member [member ..]: 添加元素
*srem key member [member ..]: 删除元素
*smember key: 返回一个集合中的所有元素
*sinter key1 key2: 返回两个集合共同的元素,key1和key2分别代表两个集合
*sinterstore key1 key2 key3: 取key1和key2的交集,并存放到key3集合中
*sunion key1 key2: 返回一个合并后的集合
*sunionstore key1 key2 key3: 合并key1和key2集合,并存放到key3集合中 
```
### 5.1.5.Pub/Sub
``` java
*subscribe news: news表示一个频道
       返回第一行固定为 subscribe,第二行固定为频道的名称,第三行表示总共有多少个订阅者
*publish: 向终端发送一条信息
       例: punlish news “hello”
       注: redis-cli一旦使用subscribe,将一直等待频道的消息并输出,如果想使用redis-cli中的其他命令,就必须重新打开一个终端
*psubscribe:  另一种订阅模式
       例: news.*: 订阅所有news.开头的频道
           new-?: 可以订阅news-1、news-2等频道
           news[123]: 订阅news-1、news-2、news-3频道
```
## 5.2.Spring Boot集成Redis
引入依赖
``` xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
``` yml
# 配置application.properties
spring.redis.host = 127.0.1.1
spring.redis.password = 123456
spring.redis.port = 6379
#最大连接数
spring.redis.pool.max-active = 8
```
实例:
``` java
@Autowired
private StringRedisTemplate redisClient;
…
redisClient.opsForValue().set(“testenv”,para);  //para=”123”相当于set  testenv  “123”
…
```
## 5.3.使用StringRedisTemplate
StringRedisTemplate继承于RedisTemplate
### 5.3.1.opsFor
``` java
*opsForValue: 用来设置普通的Key-Value
redisClient.opsForValue().set(“testenv”,”para”)   == set  testenv  para
redisClient.opsForValue().get(“testenv”)   == get  testenv
*opsForList: 用来操作List结构
redisClient. opsForList ().leftPush(“platform:message”,”hello”)
        == lpush platform:message hello
opsForList提供了lefPush、leftPushAll、leftPop、rightPush、rightPushAll、rightPop等操作,也提供了range于size操作,用于查看List的长度
*posForHash: 用来操作Hash数据结构
          redisClient.opsForHash().put(“cache”,key,value)  == hset cache key value
```
### 5.3.2.绑定Key的操作
``` java
BoundListOperations operations = redisClient.boundListOps(“somekey”);
//此后的操作就不需要在提供key参数
*boundValueOps  -->  BoundValueOperations      //value相关操作
*boundListOps  -->  BoundListOperations            //List相关操作
*boundHashOps  -->  BoundHashOperations        //Hash相关操作
*boundSetOps  -->  BoundSetOperations            //Set相关操作
*boundZSetOps  --> ZSetOperations                  //Sorted Set相关操作
*boundGeoOps  -->  BoundGeoOperations         //Geo,地理信息相关操作
```
### 5.3.2.RedisConnection
用于低级别API操作Redis,具体实现有JRedis或者Lettuce
### 5.3.3.Pub/Sub
``` java
*convertAndSend(): 发送一条消息
*redisClient. convertAndSend(“news”,”hello,world”)
*MessageListener的onMessage方法:订阅消息
public class MyRedisChannelListener implements MessageListener{
     public void onMessage(Message message，byte[] pattern){
           byte[] channel = message.getChannel();
           byte[] bs = message.getBody();
           try{
                String content = new String(bs,”UTF-8”);
                String p = new String(channel,”UTF-8”);
                System.out.println(“get ”+content+” from”+p);
           } catch (UnsupportedEncodingException e){
                e.printStachTrace();
           }
     }
}
```
注:编写好MessageListener后,还需要添加一些固定的Java代码来设置监听器
``` java
@Bean
MesssageListenerAdapter listenerAdapter(){
     return new MessageListenerAdapter(new MyRedisChannelListener());
}
@Bean
RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory,MessageListenerAdapter listenerAdapter){
     redisMessageListenerContainer container = new RedisMessageListenerContainer();
     container.setConnectionFactory(connectionFactory);
     container.addMessageListener(listenerAdapter,new PatternTopic(“news.*”));
     return container;
}
```
### 5.3.4 自定义RedisConfig示例:
``` java
//用来自定义序列化器的类

@Configuration
public class MyRedisConfig {

    // 自定义序列化为json格式
    @Bean
    public RedisTemplate<Object,Employee> empRedisTemplate(
            RedisConnectionFactory redisConnectionFactory)
            throws UnknownHostException {
        RedisTemplate<Object, Employee> template = new RedisTemplate<Object, Employee>();
        template.setConnectionFactory(redisConnectionFactory);
        Jackson2JsonRedisSerializer<Employee> ser = new Jackson2JsonRedisSerializer<Employee>(Employee.class);
        template.setDefaultSerializer(ser);
        return template;
    }

    //自定义CacheManager
    //CacheManagerCustomizers可以定制缓存的一些规则
    @Bean
    public RedisCacheManager employeeCacheManager(RedisTemplate<Object,Employee> empRedisTemplate){
        RedisCacheManager cacheManager = new RedisCacheManager(empRedisTemplate);
        cacheManager.setUsePrefix(true);
        return cacheManager;
    }

}
```
## 5.4.序列化策略
使用默认序列化策略
-(1).引入
``` java
@Autowired
@Qualifier(“redisTemplate”)
private RedisTemplate redisClient;
```
-(2).新建可序列的对象
``` java
public static class User implements java.io.Serializable{
     ...
}
```
## 5.5.自定义序列化策略
``` java
// 自定义序列化为json格式
@Bean
public RedisTemplate<Object,Employee> empRedisTemplate(
     RedisConnectionFactory redisConnectionFactory)throws UnknownHostException {
        
     RedisTemplate<Object, Employee> template = new RedisTemplate<Object, Employee>();
     template.setConnectionFactory(redisConnectionFactory);
     Jackson2JsonRedisSerializer<Employee> ser = new Jackson2JsonRedisSerializer<Employee>(Employee.class);
     template.setDefaultSerializer(ser);
     return template;

}
```
