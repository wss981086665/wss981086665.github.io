---
title: Spring Boot学习笔记3(Spring Boot)
date: 2019-05-04 10:15:55
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
---
# 三、Spring Boot
## (1).Spring Boot注解整理
``` java
*@SpringBootApplication: 使用此注解使其成为Spring Boot应用
*main中添加: SpringApplication.run(类名.class,args);
*@Controller是Spring MVC注解,表示此类用于负责处理Web请求
*@RequestMapping是Spring MVC注解,表示如果请求路径匹配,备注解的方法将被调用
*@ResponseBody表示此方法返回的是文本而不是视图名称
*@RestController相当于Controller和ResponseBody   (RESTFul:一种架构风格)
```

## (2).创建SpringBoot工程
### 1.创建Maven工程
### 2.增加Web支持
``` xml
1.在pom.xml中添加以下内容,使工程变成SpringBoot应用
   <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
   </dependency>
2.SpringBoot通过Starter来提供系统极服务,搭建Web应用  
 <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
</dependency>
3.使用热部署
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
</dependency>
```
## (3).Maven
``` xml
Maven的核心是pom.xml,用XML方式描述了项目类型,pom通常有以下元素:
*groupId: 表示项目所属的组,通常是一个公司或者组织的名称
*artifactId: 项目唯一的标识。groupId和artifactId能唯一标识一个项目或一个库
*packaging: 项目的类型,常有jar和war两种
*version: 项目的版本号
*modelVersion: 代表pom文件的Maven的版本
*dependencies: 包含多个dependency,用来声明项目依赖,pom最核心的部分
*dependency: 包含在dependencies中,用来声明项目依赖。比如用到MySQL驱动:
 <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>6.0.5</version>
        <scope>provided</scope>
</dependency>
```
*scope: 代表此类库与项目的关系,默认是compile,也就是编译和打包都需要此类库。test表示仅仅在单元测试的时候需要,provided表示在编译阶段需要此类库,但打包阶段不需要,runtime表示在编译和打包的时候都不需要,但运行是需要
*build: 此项在pom中可选,build包含多个插件plugin，用来辅助构建
## (4).数据库访问
1.配置数据源
(1).集成HikariCP
//HikariCP: 数据库连接池。当然也有其他选择
在pom中加入如下依赖
``` xml
<dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>2.6.1</ version >
</dependency>
  配置连接数据库的基本信息以供HikariCP使用:
spring.datasource.url = jdbc:mysql://127.0.0.1:3306/orm?
useUnicode = true&characterEncoding = UTF-8
spring.datasource.username = root
spring.datasource.password = 123456
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
在pom中添加mysql依赖
<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>6.0.5</ version >
</dependency>
```

