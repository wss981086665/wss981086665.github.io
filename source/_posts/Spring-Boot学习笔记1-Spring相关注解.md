---
title: Spring Boot学习笔记1(Spring相关注解)
date: 2019-05-04 10:12:25
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
---
SpringBoot学习笔记
*AOP: 指面向切面编程,通过预编译方式或者运行时刻对目标对象动态的添加功能

## 一、Spring相关

### (1).Spring相关注解
``` java

[1.@configuration](mailto:1.@configuration)： 注解成功引起Spring容器的注意

[2.@Aspect](mailto:2.@Aspect): 让Spring容器知道,这是一个AOP类

[3.@Around](mailto:3.@Around): 是AOP的一种具体方式,简单地说它能对目标方法调用前和调用后进行处理

[4.@within(@org.springframework.stereotype.Controller*)](mailto:4.@within(@org.springframework.stereotype.Controller*)): 可以理解为对所有使用@Controller注解的类进行AOP

[5.@annotation(function)](mailto:5.@annotation(function)): 对具有function参数对应的注解方法进行AOP

[6.@Repository](mailto:6.@Repository): 声明此类是一个数据库或其他NoSQL访问类

[7.@RestController](mailto:7.@RestController): 同Controller,用于REST服务

[7.@Component](mailto:7.@Component): 声明此类是一个Spring管理的类,通常用于无法用上述注解的Spring管理类

[8.@Configuration](mailto:8.@Configuration)：声明此类是一个配置类,通常与注解@Bean配合使用

[9.@Bean](mailto:9.@Bean): 作用在方法上,声明该方法执行的返回结果是一个Spring容器管理的Bean

注: Spring负责实例化Bean,开发者可以提供一系列回调函数,用于进一步配置Bean,包括@PostConstruct注解和@PreDestory注解

[10.@PreDestory](mailto:10.@PreDestory): 在容器被销毁之前,调用被@ PreDestory注解的方法二、SpringBoot基础
```
