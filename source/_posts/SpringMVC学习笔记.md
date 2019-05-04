---
title: SpringMVC学习笔记
date: 2019-04-26 09:36:26
tags:
---
# SpringMVC学习笔记
## 第一部分 HelloWorld
#### 文件结构
![文件结构图.png](https://upload-images.jianshu.io/upload_images/13687958-5d875e76bb3eacdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### web.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--配置DispatcherServlet-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置配置DispatcherServlet的一个初始化参数：配置SpringMVC配置文件的位置和名称 -->
        <!--
            实际上也可以不通过contextConfigLocation来配置SpringMVC的配置文件，而使用默认的，
            默认的配置文件为：/WEB-INF/<servlet-name>-servlet.xml
         -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```
#### index.jsp
``` html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2019/4/22
  Time: 15:15
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
     <a href="helloworld">helloworld</a><br>
     <a href="/springmvc/testRequestMapping">Test RequestMapping</a>
  </body>
</html>
```
#### success.jsp
``` html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2019/4/22
  Time: 15:40
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>success</title>
</head>
<body>
    <h1>Hello SpringMVC</h1>
</body>
</html>
```
#### HelloWorld.java
``` java
package com.wss.hellomvc;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class Helloworld {

    @RequestMapping("/helloworld")
    public String hello() {
        System.out.println("helloworld");
        return "success";
    }

}
```
#### SpringMVCTest.java
``` java
package com.wss.hellomvc;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("springmvc")
@Controller
public class SpringMVCTest {

    public static final String SUCCESS = "success";
    @RequestMapping("/testRequestMapping")
    public String testRequestMapping() {
        System.out.println("testRequest");
        return SUCCESS;
    }

}
```
#### springmvc.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 配置自动扫描的包 -->
    <context:component-scan base-package="com.wss.hellomvc"></context:component-scan>

    <!-- 配置视图解析器：如何把handle方法返回值解析为实际的物理视图-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

</beans>
```
至此，SpringMVC的HelloWorld就已经完成了，具体怎么运行，请先百度一下（实际上配置了tomcat就基本上没什么大问题）。这里有一个小问题的解决方法，读者可以参考一下

![一种错误的排查方案.png](https://upload-images.jianshu.io/upload_images/13687958-37ba3dcb7aa155b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
