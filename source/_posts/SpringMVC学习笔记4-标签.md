---
title: SpringMVC学习笔记4(标签)
date: 2019-05-04 09:57:26
categories: 
- Spring MVC
tags: 
- Spring MVC
- 后端
---
# 标签
#### 1.mvc:view-controller
> mvc:view-controller可以直接将请求映射到静态页面
###### 示例:
 ``` xml
springmvc.xml 页面添加：

<!-- 配置直接转发的页面 -->
    <!-- 可以直接响应转发的页面，而无需再经过 Handle 的方法 -->
    <!-- 注意：如果只配置 mvc:view-controller 标签，会使 @RequestMapping 标签失效，添加下面的 mvc:annotation-driven 标签即可解决-->
    <mvc:view-controller path="/success" view-name="success"/>

    <!-- 在实际开发中通常需要配置 MVC:annotation-driven 标签 -->
    <mvc:annotation-driven></mvc:annotation-driven>
```
> 注意：mvc:view-controller标签必须搭配mvc:annotation-driven标签使用，否则会使@RequestMapping标注的请求失效
