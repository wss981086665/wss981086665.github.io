---
title: Spring Boot学习笔记2(Spring MVC)
date: 2019-05-04 10:13:55
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
- Spring MVC
---
# 二、Spring MVC
## (1).集成MVC框架
### 1.引入依赖
-使用的模版技术:Beetl
``` xml
<dependency>
        <groupId>com.ibeetl</groupId>
        <artifactId>beetl-framework-starter</artifactId>
        <version>1.1.15.RELEASE</version>
</dependency>
```
## 2.MVC知识点
### (1).HTTP method匹配
*GET: 用来获取URL对应的内容
*POST: 用来向服务器提交信息
*HEAD: 同GET,但不返回消息体,通常用于返回URL对应的元信息,如过期时间。搜索引擎通常用HEAD来获取网页信息
*PUT: 同POST,用来向服务器提交信息,但语义上更像一个更新操作
*DELETE: 删除对应的资源信息
*PATCH: 类似于PUT方法,表示信息的局部更新
### (2).consumes和produces
*consumes意味着请求的HTTP头的Content-Type媒体类型与consumes的值匹配才能调用此方法
*produces属性对应于HTTP请求的Accept字段,只有匹配得上的方法才能被调用
### (3).params和header匹配
从请求参数或者HTTP头中提取值来进一步确定调用的方法
## 3.方法参数
``` java
*@PathVariable: 可以将URL中的值映射到方法参数中
*JavaBean: 将HTTP参数映射到JavaBean对象
*MultipartFile: 用于处理文件上传
*WebRequest或者NativeWebRequest,类似于ServletRequest,但做了一定封装
*java.io.InputStream/java.io.Reader,用来获取Servlet API中的InputStream/Reader
*java.io.OutputStream/java.io.Writer,用来获取Servlet API中的OutputStream /Writer
*HttpMethod: 枚举类型,对应HTTP Methd,如POST、GET
*@MatrixVariable: 矩阵变量
*@RequestParam: 对应于HTTP请求的参数,自动转化为参数对应的类型
*@RequestHeader: 对应于HTTP请求头参数,自动转化为对应的类型
*@RequestBody: 自动将请求内容转化为指定的对象,默认使用HttpMessageConverters来转化
*@RequestPart: 用于文件上传,对应的HTTP于协议的multipart/form-data
*@SesssionAttribute: 该方法标注的变量来自于Session的属性
*@RequestAttribute: 该标注的变量来自于request的属性
*@InitBinder: 用在方法上,说明这个方法会注册多个转化器,用来个性化地将HTTP请求参数转化成对应的Java对象,也可以实现WebBindingInitializer接口来用于SpringBoot应用所需要的dataBinder
*BindingResult和Errors: 用来处理绑定过程中的错误
``` java
## 4.验证框架
Spring Boot支持JSR-303、Bean验证框架,默认使用的是Hibernate validator
JSR-303：
``` java
空检查:
*@NULL: 验证对象是否为空
*@NotNULL: 验证对象不为空
*@NotBlank: 验证字符串不为空或者不是空字符串,””和” ” 都会验证失败
*NotEmpty: 验证对象不为null,或者集合不为空
长度检查:
*@Size(min=,max=): 验证对象长度,可支持字符串,集合
*@Length: 字符串大小
数值检测:
*@Min: 验证数字是否大于等于指定的值
*@Max: 验证数字是否小于等于指定的值
*@Digits: 验证数字是否符合指定格式,如@Digits(integer=9,fraction=2)
*@Range: 验证数字是否在指定的范围内,如@Range(min=1,max=1000)
其他:
@Email: 验证是否为邮件格式,为null则不做校验
@Pattern: 验证String对象是否符合正则表达式的规则
JavaBean示例:
public class WorkInfoForm {
@NotNull
 Long id;
@Size(min=3,max=20)
String name;
@Email
String email;
}
```
## 5.WebMvcConfigurer

``` java
//拦截器
//使用addInterceptors方法可以设置多个拦截器
public void addInterceptors(InterceptorRegistry regustry){
//增加一个拦截器,检查会话,URL以admin开头的都使用此拦截器
        registry.addInterceptor(new 
SessionHandlerInterceptor()).addPathPatterns(“/admin/**”);
}
class SessionHandlerInterceptor implements HandleInterceptor{
   …………
}

//跨域访问
//允许所有跨域访问,或者更为精确的控制
public void addCorsMappings(CorsRegistry registry){
        registry.addMapping(“/api/**”)
        .allowedOrigins(“http://domain2.com”)
        .allowedMethods(“POST”,”GET”);
}
//仅仅允许来自domain2.com的访问,并且限定访问路径为/api、方法是POST或GET
```
