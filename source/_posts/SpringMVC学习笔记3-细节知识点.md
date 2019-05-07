---
title: SpringMVC学习笔记3(细节知识点)
date: 2019-05-04 09:55:10
categories: 
- Spring MVC
tags: 
- Spring MVC
- 后端
---
# 第三部分 细节知识点
## 1.REST
什么是REST：
资源状态转化，读者可自行百度了解，这里不再详述：

![REST.png](https://upload-images.jianshu.io/upload_images/13687958-a2ac870a8dc1173a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 配置org.springframework.web.filter.HiddenHttpMethodFilter
``` xml
<!-- 配置org.springframework.web.filter.HiddenHttpMethodFilter: 可以把POST请求转为DELETE或POST请求 -->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
### 用例:
``` jsp
index.jsp 添加以下代码
<a href="springmvc/testRest/1">Test Rest GET</a><br>

  <form action="springmvc/testRest" method="post">
      <input type="submit" value="TestRest POST">
  </form>
  <br>

  <form action="springmvc/testRest/1" method="post">
      <input type="hidden" name="_method" value="DELETE">
      <input type="submit" value="TestRest DELETE">
  </form>
  <br>

  <form action="springmvc/testRest/1" method="post">
      <input type="hidden" name="_method" value="PUT">
      <input type="submit" value="TestRest PUT">
  </form>
  <br>
```
``` java
SpringMVCTest.java 添加以下代码

/*
    * Rest风格的URL
    * 以CRUD为例：
    * 新增：/order POST
    * 修改：/order/1 PUT
    * 获取：/order/1 GET
    * 删除：/order/1 DELETE
    *
    * 如何发送PUT请求与DELETE请求呢?
    * 1.需要配置HiddenHttpMethodFilter
    * 2.需要发送POST请求
    * 3.需要在发送POST请求时携带一个name="_method"的隐藏域，值为PUT或DELETE
    *
    * 在SpringMVC的目标方法中如何得到id呢？
    * 使用@PathVariable注解
    * */

@RequestMapping(value = "/testRest/{id}",method = RequestMethod.GET)
    public String testRest(@PathVariable Integer id) {
        System.out.println("testRest GET: " + id);
        return SUCCESS;
    }

    @RequestMapping(value = "/testRest",method = RequestMethod.POST)
    public String testRest() {
        System.out.println("testRest POST");
        return SUCCESS;
    }

    @RequestMapping(value = "/testRest/{id}",method = RequestMethod.DELETE)
    public String testRestDelete(@PathVariable Integer id) {
        System.out.println("testRest DELETE: " + id);
        return SUCCESS;
    }

    @RequestMapping(value = "/testRest/{id}",method = RequestMethod.PUT)
    public String testRestPut(@PathVariable Integer id) {
        System.out.println("testRest PUT: " + id);
        return SUCCESS;
    }
```
## 2.POJO对象绑定请求参数
- SpringMVC会按请求参数名和POJO属性名进行自动匹配，自动为该对象填充属性值。支持级联属性。如dept.deptId、dept.address.tel等
### 测试：
- User.java（省略setter与getter、toString方法）
``` java
public class User {

    private String username;
    private String password;
    private String email;
    private int age;

    private Address address;

}
```
- Address.java（省略setter与getter、toString方法）
``` java
public class Address {

    private String province;
    private String city;

}
```
- index.jsp添加
``` jsp
<form action="springmvc/testPojo" method="post">
      username: <input type="text" name="username"/>
      <br>
      password: <input type="password" name="password"/>
      <br>
      email: <input type="text" name="email"/>
      <br>
      age: <input type="text" name="age"/>
      <br>
      province: <input type="text" name="address.province"/>
      <br>
      city: <input type="text" name="address.city"/>
      <br>
      <input type="submit" value="Submit"/>
  </form>
```
- SpringMVCTest.java添加
``` java
/*
    * SpringMVC 会按请求参数名和POJO属性名进行自动匹配
    * 自动为该对象填充属性值。支持联机属性
    * 如dept.deptId、dept.address.tel等
    * */
    @RequestMapping(value = "/testPojo",method = RequestMethod.POST)
    public String testPojo(User user) {
        System.out.println("testPojo: " + user);
        return SUCCESS;
    }
```
## 3.使用Servlet API作为参数
- 可以使用Servlet原生的API作为目标方法的参数  具体支持一下类型
用例：
``` java
/*
    * 可以使用Servlet原生的API作为目标方法的参数  具体支持一下类型
    * HttpServletRequest
    * HttpServletResponse
    * HttpSession
    * java.security.Principal
    * Local InputStream
    * OutputStream
    * Reader
    * Writer
    * */
    @RequestMapping("/testServletAPI")
    public void testServletAPI(HttpServletRequest request,
                                 HttpServletResponse response,
                                 Writer out) throws IOException {
        System.out.println("testServletAPI, " + request + ", " + response);
        out.write("Hello Spring MVC");
//      return SUCCESS;
    }
```
## 4.处理模型数据
Spring MVC提供以下几种途径输出模型数据：
![处理模型数据.png](https://upload-images.jianshu.io/upload_images/13687958-6a570b5fe23b8063.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### - ModelAndView
![ModelAndView.png](https://upload-images.jianshu.io/upload_images/13687958-d6c762b1edd32043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 示例：
``` java
SpringMVCTest.java添加:
/*
    * 目标方法的返回值可以是 ModelAndView 类型。
    * 其中可以包含视图和模型信息
    * Spring MVC 会把 ModelAndView 的 model 中数据放入到 request 域对象中
    * */
    @RequestMapping("/testModelAndView")
    public ModelAndView testModelAndView() {
        String viewName = SUCCESS;
        ModelAndView modelAndView = new ModelAndView(viewName);

        // 添加模型数据到 ModelAndView 中.
        modelAndView.addObject("time",new Date());

        return modelAndView;
    }
```
``` jsp
index.jsp添加:
<a href="springmvc/testModelAndView">Test ModelAndView</a><br>

sussess.jsp添加:
time:${requestScope.time }
```
- 注意:这里有一种错误可能使页面无法跳转(导包问题)：
```
正确: :import org.springframework.web.servlet.ModelAndView;
错误: :import org.springframework.web.portlet.ModelAndView;
```
###### - Map及Model
![Map及Model.png](https://upload-images.jianshu.io/upload_images/13687958-afd052e4b62a804d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 示例:
``` java
SpringMVCTest.java添加:
/*
    * 目标方法可以添加 Map 类型(实际上也可以是 Model 类型或ModelMap类型)
    * */
    @RequestMapping("/testMap")
    public String testMap(Map<String,Object> map) {
        map.put("names", Arrays.asList("Tom","Jerry","Mike"));
        return SUCCESS;
    }
```
``` jsp
index.jsp添加:
<a href="springmvc/testMap">Test Map</a><br>

success.jsp添加:
names:${requestScope.names }
```
## 5.视图解析器
![视图与视图解析器.png](https://upload-images.jianshu.io/upload_images/13687958-e1b341ab57d343d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![视图.png](https://upload-images.jianshu.io/upload_images/13687958-37c413692e036b0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![常用的视图实现类.png](https://upload-images.jianshu.io/upload_images/13687958-7f918ebd7b828b2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 5.自定义视图解析器
> 作用：用来解析自定义视图
###### 常见的视图解析器实现类
![常见的视图解析器实现类.png](https://upload-images.jianshu.io/upload_images/13687958-ab4c8ac37b0b0ab2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
###### 这里以使用 BeanNameViewResolver 为例：
示例：
> 首先需要创建一个包views，然后创建一个HelloView类，HelloView.java实现代码如下
``` java
package com.wss.views;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.View;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Date;
import java.util.Map;

// @Component 将视图放入 IOC 容器里
@Component
public class HelloView implements View {
    @Override
    public String getContentType() {
        return "text/html";
    }

    @Override
    public void render(Map<String, ?> map, HttpServletRequest request,
                       HttpServletResponse response) throws Exception {
        response.getWriter().print("hello View, time: " + new Date());
    }
}
```
``` xml
springmvc.xml 添加：

<!-- 这一步必不可少。如果没有这一行，SpringMVC将无法从views包中扫描到需要解析的类 -->
<context:component-scan base-package="com.wss.views"></context:component-scan>

<!-- 配置视图 BeanNameViewResolver 解析器：使用视图的名字来解析视图 -->
<!-- 通过 order 属性来定义视图解析器的优先级，order值越小优先级越高 -->
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
     <property name="order" value="100"></property>
</bean>
```
``` java
SpringMVCTest.java添加：
@RequestMapping("/testView")
public String testView() {
     System.out.println("Test View");
     return "helloView";
}

index.jsp添加：
<a href="https://www.baidu.com">Go to Baidu</a>
``` 
#### 6.重定向
- 关于重定向
![关于重定向.png](https://upload-images.jianshu.io/upload_images/13687958-8a0b9f6d10879e06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
示例（redirect）:
``` java
SpringMVCTest.java 添加：

@RequestMapping("/testRedirect")
public String testRedirect() {
     System.out.println("testRedirect");
     return "redirect:/index.jsp";
}
```
``` jsp
index.jsp 添加：

<a href="/springmvc/testRedirect">Test Redirect</a>
```
## 7.数据的格式化
![数据格式化.png](https://upload-images.jianshu.io/upload_images/13687958-1bbba5d7da14a6ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 第一步：添加下述标签(必须)：
``` xml
<!-- 在实际开发中通常需要配置 MVC:annotation-driven 标签 -->
    <mvc:annotation-driven></mvc:annotation-driven>
```
- 第二步：在目标属性上添加注解：
``` java
例(@DateTimeFormat)：

@DateTimeFormat(pattern="yyyy-MM-dd")
private Date birth;
```
## 8.数据校验
### 1.如何校验？注解？
``` 
a.使用JSR 303验证
b.加入Hibernate Validator验证框架
c.在SpringMVC配置文件中添加<mvc:annotation-driven></mvc:annotation-driven>
d.需要在bean的属性上添加对应的注解
e.在目标方法bean类型参数的前面添加@Valid注解
```
> 注意：需要引入相应的jar包
> OpenSource -> hibernate-validator文件夹里的jar文件
> OpenSource -> hibernate-validator -> lib -> required里的部分文件:
> -------classmate.jar
> -------jboss-logging.jar
> -------validation-api.jar
### 2.验证出错转向那一个页面？
> 注意：需校验的Bean对象与其绑定结果对象或错误对象成对出现时，他们之间不允许声明其它入参
### 3.错误消息？如何显示，如何把错误消息进行国际化
- JSR 303
![JSR 303.png](https://upload-images.jianshu.io/upload_images/13687958-66540799726dd786.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Hibernate Validator扩展注解
![Hibernate Validator扩展注解.png](https://upload-images.jianshu.io/upload_images/13687958-c4dbaf6745262c2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- SpringMVC数据校验
![SpringMVC数据校验.png](https://upload-images.jianshu.io/upload_images/13687958-9b16cb1996ab1215.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 9.JSON数据实现原理
- 处理JSON
![处理JSON.png](https://upload-images.jianshu.io/upload_images/13687958-9633abe62dd31b78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 负责处理请求与接受信息的对象
![HttpMessageConverter<T>.png](https://upload-images.jianshu.io/upload_images/13687958-66f10d6d002f3e5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![HttpMessageConverter处理过程.png](https://upload-images.jianshu.io/upload_images/13687958-33b4bc10dbbe830e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- HttpMessageConverter<T>
![HttpMessageConverter<T>.png](https://upload-images.jianshu.io/upload_images/13687958-8c5380fd3f463889.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 10.文件上传
- 文件上传
![文件上传.png](https://upload-images.jianshu.io/upload_images/13687958-db8dbba28746fc0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 配置MultipartResolver
![图片.png](https://upload-images.jianshu.io/upload_images/13687958-ba4e1cc72c3888d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 11.拦截器
- 自定义拦截器
![自定义拦截器.png](https://upload-images.jianshu.io/upload_images/13687958-f699c9711ad96977.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 示例：
``` java
首先创建interceptors，然后创建FirstInterceptor 类，FirstInterceptor .java文件内容如下：
--------------------------------------------------------------------------------------------------------------
package com.wss.interceptors;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class FirstInterceptor implements HandlerInterceptor {

    /*
    * 该方法在目标方法之前被调用
    * 若返回值为true，则继续调用后续的拦截器和目标方法
    * 若返回值为false，则不会再调用后续的拦截器和目标方法
    *
    * 可以考虑做权限，日志，事务等
    * */
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        System.out.println("FirstInterceptor preHandle");
        return true;
    }

    /*
    * 调用目标方法之后，但渲染视图之前
    * 可以对请求域中的属性或视图做出修改
    * */
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
        System.out.println("FirstInterceptor postHandle");
    }

    /*
    * 渲染视图之后被调用，释放资源
    * */
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
        System.out.println("FirstInterceptor afterCompletion");
    }
}

```
``` xml
springmvc.xml添加：

<mvc:interceptors>
     <!-- 配置自定义的拦截器 -->
     <bean class="com.wss.interceptors.FirstInterceptor"></bean>
</mvc:interceptors>
```
- 拦截器的配置
``` xml
在springmvc.xml中做出如下改变：

<mvc:interceptors>

<!-- *************************************************************************************** -->
        <!-- 配置拦截器(不)作用的路径 -->
        <mvc:interceptor>
            <mvc:mapping path="/emps"/>
            <bean class="com.wss.interceptors.FirstInterceptor"></bean>
        </mvc:interceptor>
<!-- *************************************************************************************** -->

        <!-- 配置自定义的拦截器 -->
        <bean class="com.wss.interceptors.FirstInterceptor"></bean>
    </mvc:interceptors>
```
#### 11.Spring整合SpringMVC
> 需要进行Spring整合SpringMVC吗？
> 还是否需要再加入Spring的IOC容器？
> 是否需要web.xml文件中配置启动Spring IOC容器的ContextLoaderListener？

1.需要：通常情况下，类似于数据源、事务、整合其它框架都是放在Spring的配置文件中（而不是放在SpringMVC的配置文件中）。实际上放入Spring配置文件对应的IOC容器中还有Service和Dao
2.不需要：都放入SpringMVC的配置文件中。也可以分多个Spring的配置文件，然后使用import节点导入其他的配置文件






