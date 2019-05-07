---
title: SpringBoot7(Junit测试)
date: 2019-05-04 10:23:50
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
---
## 七、Junit 测试
### Junit单元测试
> 单元测试是针对项目中单一目标（一个类或几个方法）的测试。单元测试可以有效保证代码质量和产量。
###### Junit 中的常用注解
- @BeforeClass：初始化测试类，当前测试类中所有测试方法执行之前执行该方法，只执行一次，且必须为 static void
- @Before：初始化测试方法，当前测试类中每个测试方法执行前都要执行该方法
- @Test：测试方法，主要参数如下：
timeout设置超时时间，单位毫秒，例如：@Test(timeout=2000)
expected设置期望异常，例如：@Test(expected=NullPointerException.class)
- @After：释放资源，当前测试类中每个测试方法执行之后都要执行该方法
- @AfterClass：释放资源，当前测试类中所有测试方法执行之后执行该方法，只执行一次，且必须为 static void
- @Ignore：忽略被注解的测试方法
###### 一个有两个测试方法的测试类其中方法的执行顺序为：
> @BeforeClass –> @Before –> @Test –> @After –> @Before –> @Test –> @After –> @AfterClass
### Junit套件测试
> 在实际项目中，随着产品需求的不断增加，业务代码会飞速增长，单元测试类也会随之增加，如果我们继续一个个的单独运行测试类，可想而知我们的工作量会非常之大。为了解决这个问题，Junit 提供了一种批量运行测试类的方法，即套件测试。测试套件的写法非常简单，创建一个空类作为测试套件的入口，使用如下两个注解配置即可。

- @RunWith： 设置测试运行器 ，缺省值org.junit.runner.Runner，套件测试需要设置为Suite.class
- @SuiteClasses： 设置多个测试类
### SpringBoot测试
> 在 Spring Boot 中，我们可以通过添加spring-boot-starter-test依赖快速开启和使用它。创建一个普通类，使用如下三个注解配置即可。
- @RunWith： 设置测试运行器 ，缺省值org.junit.runner.Runner，Spring Boot 测试需要设置为SpringRunner.class
- @SpringBootTest： 获取 Spring Boot Web 应用启动类，加载配置
- @AutoConfigureMockMvc： 自动注入MockMvc
