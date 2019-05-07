---
title: Spring Boot学习笔记4(配置)
date: 2019-05-04 10:17:03
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
---
## 四、Spring Boot配置

### 4.1.配置Spring Boot

#### 4.1.1.服务器配置(properties文件)

``` yml
*更换端口: server.port = 9090

 也可以在命令行中指定启动端口,比如传入参数—server.port=9000 :

 java –jar bootsample.jar –server.port=9000

*SpringBoot默认为应用配置的上下文访问目录是“/”,可以通过配置文件或者命令行,配置server.context-path:

 server.servlet.Path=/config

常用的服务器配制的属性如下:

*server.address: 服务器IP绑定地址,如果主机有多个网卡,可以绑定一个IP地址

*server.session.timeout: 会话过期时间,以秒为单位

*server.err.path: 服务器出错后的1处理路径/error

*server.servlet.contextpath: Spring Boot应用的上下文

*server.port: Spring Boot英勇监听端口
```
#### 4.1.2使用其他Web服务器
``` yml

*server.tomcat.*包含了Tomcat的相关配置

 #打开Tomcat访问日志

 server.tomcat.accesslog.enabled=false

 #访问日志所在的目录

 server.tomcat.accesslog.directory=logs

 #允许HTTP请求缓存到请求队列的最大个数,默认不受限制

 server.tomcat.accept-count=

 #最大连接数,默认不限制,如果一旦连接数到达,剩下的连接将会保存到请求缓存队列里,也就是accept-count指定队列

 server.tomcat.max-connections=

 #最大工作线程数

 server.tomcat.max-threads=

 #HTTP POST内容最大长度

 server.tomcat.max-http-post-size
```
#### 4.1.3 MySql配置(yml文件)
``` yml
#=========MySQL基础配置===========#
# MySQL driver-class
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
# MySQL服务器地址、端口、数据库名称、编码
    url: jdbc:mysql://localhost:3306/MySQL1
# MySQL用户名
    username: admin
    schema:
     - classpath:sql/user.sql
# MySQL密码
    password: ******（此处为数据库连接密码）
```
#### 4.1.4 Redis配置(yml文件)
``` yml
#=========Redis基础配置===========#
# Redis数据库索引（默认为0）
  redis:
    database: 0
# Redis服务器地址
    host: 127.0.0.1
# Redis服务器连接端口
    port: 6379
# Redis服务器连接密码（默认为空）
    password: 123456

#=========Redis线程池配置==========#
# 连接池最大连接数（使用负值表示没有限制）
    pool:
      max-active: 200
# 连接池最大阻塞等待时间（使用负值表示没有限制）
      max-wait: -1
# 连接池中的最大空闲连接
      max-idle: 10
# 连接池中的最小空闲连接
      min-idle: 0
# 连接超时时间（毫秒）
    timeout: 1000
```
#### 4.1.5 其他
- 开启驼峰命匹配规则(properties文件)
``` yml
mybatis.configuration.map-underscore-to-camel-case=true
```
- 开启数据库访问日志
``` java
logging.level.com.wss.cache.mapper=debug
// 注意: com.wss.cache.mapper 是 src/main/java 下的mapp文件所在的路径
```
### 4.2.读取应用配置

#### 4.2.1Environment

Environment是一个通用的读取应用程序运行时环境变量的类,可以读取application.properties、命令行输入参数、系统属性、操作系统环境变量等,可以通过Spring容器自动注入,比如在Spring管理的Bean中:
``` java
  @Configuration
  public class EnvConfig{

       @Autowired private Environment env;
       public int getServerPort() {
           return env.getProperty(“server.port”,Integer.classs);
       }

  }
```
其他读取的例子:
```  java
env.getProperty(“user.dir”): 程序运行的目录,如果在IDE中运行,就是工程目录,user.dir是系统属性

env.getProperty(“user.home”):执行程序的用户的home目录,user.home是系统属性

env.getProperty(“JAVA_HOME”):读取设置的环境变量(不区分大小写)

env.getProperty(“server.port”):读取server.port,来自application.properties
```

#### 4.2.2.@Value(mailto:4.2.2.@Value)
``` java
直接通过@Value注解注入一个配置信息到Spring管理的Bean中:
@RequestMapping(“/showvalue.html”)
public @ResponseBody String value(@Value(“${server.port}”) int port){
 return "port:"+port;

}
```
*注意:@Value不能在任何BeanPostProcessor和BeanFactoryPostProcessor的子类中被注入(因为@Value本身是通过AutowiredAnnotationBeanPostProcesser实现的,它是BeanPostProcessor接口的实现类)
#### 4.2.3 通过@ConfigurationProperties注入:
``` java
@ConfigurationProperties(
    prefix = "spring.redis"
)
public class RedisProperties {
    private int database = 0;
    private String url;
    private String host = "localhost";
    private String password;
    private int port = 6379;
    private boolean ssl;
    private int timeout;
    private RedisProperties.Pool pool;
    private RedisProperties.Sentinel sentinel;
    private RedisProperties.Cluster cluster;
}
```

