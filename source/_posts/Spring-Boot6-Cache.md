---
title: Spring Boot6(Cache)
date: 2019-05-04 10:21:20
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
- Cache
---
## 六、Cache

集成Spring Cache
``` xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

若要使用Spring自带的内存缓存管理器,需要添加:
``` java

 spring.cache.type = Simple

*Simple: 基于ConcurrentHashMap实现的缓存,适合单体应用或者开发环境使用

*none: 关闭缓存,比如开发阶段为为了确保功能正确,可以先禁用缓存

*redis: 使用Redis作为缓存,还需要在pom中增加Redis依赖//Redis实现一二级缓存

*Generic: 用户自定义缓存实现,用户需要实现一个org.springframework.cache.CacheManager的实现
```
其他还有JCache、EhCache 2.x、Hazelcast

### 6.1.注释驱动缓存

*需要使用注解@EnableCaching打开缓存功能
``` java
@Cacheable: 作用在方法上,触发缓存读取操作
@CacheEvict: 作用在方法上,触发缓存失效操作
@CachePut: 作用在方法上,触发缓存更新操作
@Cache: 作用在方法上，综合上面的各种操作,在有些场景下,调用业务会触发多种缓存操作
@CacheConfig: 在类上设置当前缓存的一些公共设置
```
#### [6.1.1.@Cacheable](mailto:6.1.1.@Cacheable)
> CacheManager 管理多个 Cache 组件，对缓存真正的CRUD操作在Cache组件中，每一个缓存组建由自己唯一的名字;
几个属性:
> - cacheNames/value: 指定缓存的名字
> - key: 缓存数据使用的key；可以用来指定缓存的对象。默认是使用方法参数的值。也可以用SpEL表达式:
![Cache SpEL available metadate.png](https://upload-images.jianshu.io/upload_images/13687958-05b1433852a3d03e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> - keyGenerator: key的生成器；可以自己指定key的生成器的组件id
            key/keyGenerator:二选一使用
> - cacheManage: 指定缓存管理器；或者是cacheResolver指定缓存解析器
> - condition: 指定符合条件的情况下才缓存
例：
> ```
> @Cacheable(value = {"emp","xxx"},keyGenerator = "myKeyGenerator",
>                condition="#id>0 and #root.methodName eq 'aaa'")
> public Employee getEmp(Integer id){
>     System.out.println("查询"+id+"号员工");
>     Employee emp = employeeMapper.getEmpById(id);
>     return emp;
> }
> ```
> - unless: 否定缓存，当unless指定的条件为true，方法的返回值就不会被缓存
> - sync: 是否使用异步模式
> - 使用自定义的keyGenerator—自定义keyGenerator：
> ``` java
> @Configuration
> public class MyCacheConfig {
>     @Bean("myKeyGenerator")
>     public KeyGenerator keyGenerator(){
>         return new  KeyGenerator(){
>             public Object generate(Object target, Method method,Object... params){
>                 return method.getName()+"["+ Arrays.asList(params).toString()+"]";
>             }
>         };
>     }
> }
> ```

注解声明了方法的结果是可缓存的,如果缓存还在,则目标方法不会被调用,直接取缓存。可以为方法声明多个缓存,如果至少有一个缓存有缓存想项,则其缓存项将被返回：
``` java
 @Cacheable({“Menu”,”menuExt”})
 public Menu findMenu(Long menuId){…}
```
注意:对于不同的缓存实现,最好将缓存对象实现序列化Serializable接口,这样可以无缝切换到分布式缓存系统

#### 6.1.2.Key生成器
(1).缓存的Key非常重要,Spring使用了KeyGenerator类来根据方法参数生成Key
- 如果只有一个参数,那么这个参数就是Key          
- 如果没有参数,则返回SimpleKey.EMPTY
- 如果有多个Key,则返回包含多个参数的SimpleKey
``` java
@Cacheable(“user_function”)
public boolean canAcessFunction(Long userId,Long orgId,String functionCode){
    ...
}
```
(2).使用ApEL表达式来指定Key比自定义KeyGenerator更简单
``` python
@Cacheable(cacheNames=”org”,key=”#orgId”)
public Org findOrg(Long orgId , boolean checkCrmSystem){
    ...
}
@Cacheable(cacheNames=”org” , key=”#user.orgId”)
public Org findOrg(User user) {
    ...
}
```
#### [6.1.3.@CachePut](mailto:6.1.3.@CachePut)
``` java
    /*
    *@CachePut:即调用方法，又更新缓存数据
    * 修改了数据库的某个数据，同时更新缓存;
    * 运行时机:
    *    1.先调用目标方法
    *    2.把调用方法的结果缓存起来
    *  测试步骤
    *    1.查询一号员工，查到的结果会放到缓存中
    *    2.以后查询还是之前的结果
    *    3.更新1号员工？【lastName:zhangsna;gender:0】
    *                 将方法的返回值也放进缓存了；
    *                 key:传入的employee对象  值：返回的employee对象
    *                 ***必须要指定id-->key = "#employee.id";
    *                         或者是-->key = "#result.id";使用返回后的id
    *                    @Cacheable是不能用#result
    */
```
注解CachePut总是会执行方法主体,并且使用返回的结果更新缓存,其他同Cacheable
``` java
 @CachePut(cacheNames=”org” , key=”#data.id”)
 public Org updateOrg(Org data) { … }
```
#### [6.1.4.@CacheEvict](mailto:6.1.4.@CacheEvict)
注解CacheEvict用于删除缓存项或清空缓存,CacheEvict可以指定多个缓存名字来清空多个缓存。
- 清除单个缓存
``` java
 @CacheEvict(cacheNames=”user”,key=”#id”)
 public void updateUser(Long id , int status) { … }
```
注意:CacheEvict只能清除不能加载,只有想用的Cacheable方法被调用后才会加载最新缓存项
- 清除全部缓存(重新加载后,会清空config缓存)
``` java
 @CacheEvict(cacheNames=”config”,allEntries=true)
 public void loadConfig() { … }
```
- beforeInvocation = false: 缓存的清楚是否在方法之前执行，默认(false)代表是在方法之后执行；如果方法执行执行过程中出现异常，则会清除失败
#### [6.1.5.@Caching](mailto:6.1.5.@Caching)
> 注解Caching可以混合以上各种注解,可以在Caching标签中混合`@Cacheable`、`@CachePut`、`@CacheEvict`
> - @Caching源代码实现:
> ![@Caching源代码实现.png](https://upload-images.jianshu.io/upload_images/13687958-2a50b8170cf7cad8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 示例1:
``` python
@Caching(evict = {
        @CacheEvict(cacheNames="user",key="#user.id")@CacheEvict(cacheNames="user",key="#user.id"),
        @CacheEvict(cacheNames=”userExt”,key=”#ext.id”)
})
public void updateUser(User user,UserExt ext) { … }
```
- 示例2:
``` java
    /*
    * @Caching当有些方法的缓存规则比较复杂的时候，使用Caching注解来指定多个复杂规则
    * */
    @Cacheable(value = {"emp"},key = "#id")
    @Caching(
        cacheable = {
              @Cacheable(value = "emp",key = "#lastName")
        },
        put = {
              @CachePut(value = "emp",key = "#result.id"),
              @CachePut(value = "emp",key = "#result.email")
        }
    )
    public Employee getEmpByLastName(String lastName){
        return employeeMapper.getEmpByLastName(lastName);
    }
```
#### 6.1.6.@CacheConfig

> 注解CacheConfig作用于类上,可为此类的方法的缓存注解提供默认值(cacheNames)

### 6.2使用Redis Cache

#### 6.2.1.Redis缓存原理
``` java
@Configuration

@AutoConfigureAfter(RedisAutoConfiguration.class) //确保Redis配置成功

@ConditionalOnBean(RedisTemplate.class) //使用RedisTemplate来操作缓存

//生效的条件是系统没有实现CacheMansger

@ConditionalOnMissingBean(CacheManager.class)

//注解Conditional需要联合一个CacheCondition进一步判断是否执行当前匹配类

//CacheCondition实现了Condition接口,该接口返回boolean值,用来判断是否启用该配置, CacheCondition用于判断application.properties中是否配置了spring.cache.type,取出其对应的值与CacheCondition所在的配置类进行比较,若一致,返回match

@Conditional(CacheCondition.class) 

class RedisCacheConfiguration {

 ……

}
```
Cache接口包含以下方法:
``` java
*ValueWrapper get(Object key): 根据Key获取缓存对象, ValueWrapper包含了get方法用于获取缓存对象

*void put(Object key,Object value): 设置缓存

*void evict(Object key): 根据Key值清除缓存

*void clear(): 清除所有缓存
```
### 6.3.实现Redis两级缓存
当缓存发生变化时,注解@CachePut和@CacheEvict会触发RedisCache的put(Object Key,Object Value)和evict(Object Key)操作,两级缓存需要同时更新ConcurrentHashMap和Redis缓存,而且需要通过Redis的Pub发出通知消息,其他Spring Boot应用通过Sub来接收消息,同步更新Spring Boot应用自身的一级缓存
#### 6.3.1.实现TwoLevelCacheManager
``` java
class TwoLevelCacheManager extends RedisCacheManager {
     RedisTemplate redisTemplate;
     public TwoLevelCacheManager(RedisTemplate 
          redisTemplate,RedisCacheWriter cacheWriter, RedisCacheConfiguration defaultCacheConfiguration) {
          super(cacheWriter,defaultCacheConfiguration);
          this.redisTemplate = redisTemplate;
      }
      //使用RedisAndLocalCache代替Spring Boot自带的RedisCache
      @Override
      protected Cache decorateCache(Cache cache) {
           return new RedisAndLocalCache(this, (RedisCache) cache);
      }
      //通过其他分布式节点，缓存改变
      public void publishMessage(String cacheName) {
           this.redisTemplate.convertAndSend(topicName, cacheName);
      }
      // 接受一个消息清空本地缓存
      public void receiver(String name) {
          RedisAndLocalCache cache = ((RedisAndLocalCache) this.getCache(name));
          if(cache!=null){
               cache.clearLocal();
          }
     }
 }
```
在Spring Cache中,在缓存管理器中创建好每个缓存后,都会调用decorateCache方法,这样缓存管理器子类就有机会实现自己的扩展

#### 6.3.2.创建RedisAndLocalCanch
RedisAndLocalCanch时系统的核心,它实现了Cache接口、类
class RedisAndLocalCache implements Cache {
``` java
 // 本地缓存提供
ConcurrentHashMap<Object, Object> local = new ConcurrentHashMap<Object, Object>();
RedisCache redisCache;
TwoLevelCacheManager cacheManager;
public RedisAndLocalCache(TwoLevelCacheManager cacheManager, RedisCache redisCache) {
     this.redisCache = redisCache;
     this.cacheManager = cacheManager;
}
@Override
public String getName() {
     return redisCache.getName();
}
@Override
public Object getNativeCache() {
    return redisCache.getNativeCache();
}

**关键的****get****和****put****方法如下****:**
@Override
public ValueWrapper get(Object key) {
    ValueWrapper wrapper = (ValueWrapper) local.get(key);
    if (wrapper != null) {
        return wrapper;
    } else {
        // 二级缓存取
        wrapper = redisCache.get(key);
        if (wrapper != null) {
            local.put(key, wrapper);
        }
        return wrapper;
    }
}
@Override
public <T> T get(Object key, Class<T> type) {
    return redisCache.get(key, type);
}
@Override
public <T> T get(Object key, Callable<T> valueLoader) {
    return redisCache.get(key, valueLoader);
}
@Override
public void put(Object key, Object value) {
    System.out.println(value.getClass().getClassLoader());
    redisCache.put(key, value);
    clearOtherJVM();
}
@Override
public ValueWrapper putIfAbsent(Object key, Object value) {
    ValueWrapper v = redisCache.putIfAbsent(key, value);
    clearOtherJVM();
    return v;
}
@Override
public void evict(Object key) {
    redisCache.evict(key);
    clearOtherJVM();
}
@Override
public void clear() {
    redisCache.clear();
}
public void clearLocal() {
    this.local.clear();
}
protected void clearOtherJVM() {
    cacheManager.publishMessage(redisCache.getName());
}
``` java
变量local代表了一个简单地缓存实现,使用了ConcurrentHashMap

get方法有如下逻辑实现:

*通过Key从本地取出ValueWrapper

*如果ValueWrapper存在,则直接返回

*如果ValueWrapper不存在,则调用父类RedisCache取得缓存项

*如果缓存项为空,则说明暂时无此项,直接返回空,等待@Cacheable调用业务方法获取缓存项

Put方法的实现逻辑如下:

*先调用redisCache,更新二级缓存

*调用clearOtherJVM方法,通知其他节点缓存更新

*其他节点(包括本节点)的TwoLevelCacheManager收到消息后,会调用receiver方法从而实现一级缓存

#### 6.3.3.缓存同步说明

实现Redis的Pub/Sub模式
``` java
// 定义一个redis 的频道，默认叫cache，用于pub/sub
 @Value("${springext.cache.redis.topic:cache}")
 String topicName;
// Redis message，参考Redis一章
 @Bean
 RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory,
         MessageListenerAdapter listenerAdapter) {
     RedisMessageListenerContainer container = new RedisMessageListenerContainer();
     container.setConnectionFactory(connectionFactory);
     container.addMessageListener(listenerAdapter, new   PatternTopic(topicName));
     return container;
 }
```
需要在配置文件中配置springext.cache.redis.topic 指定一个频道名字,如果没有配置,默认的频道名字是cache

配置监听器
``` java
@Bean
 MessageListenerAdapter listenerAdapter(final TwoLevelCacheManager cacheManager) {
     return new MessageListenerAdapter(new MessageListener() {
         public void onMessage(Message message, byte[] pattern) {
             byte[] bs = message.getChannel();
             try {
                 // Sub 一个消息，通知缓存管理器
                 String type = new String(bs, "UTF-8");
                 String cacheName = new String(message.getBody(),"UTF-8");
                 cacheManager.receiver(cacheName);
             } catch (UnsupportedEncodingException e) {
                 e.printStackTrace();
                 // 不可能出错，忽略
             }
         }
     });
 }
```
#### 6.3.4.将代码组合在一起
``` java
@Configuratio
public class CacheConfig {
     @Bean
     public TwoLevelCacheManager cacheManager(StringRedisTemplate redisTemplate) {
         RedisCacheWriter writer = RedisCacheWriter.lockingRedisCacheWriter(redisTemplate.getConnectionFactory());
         SerializationPair pair = SerializationPair.fromSerializer(new JdkSerializationRedisSerializer(this.getClass().getClassLoader()));
         RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig().serializeValuesWith(pair);
         TwoLevelCacheManager cacheManager = new TwoLevelCacheManager(redisTemplate,writer,config);
         return cacheManager;
 }
```
