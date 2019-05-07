---
title: SpringMVC学习笔记2(注解)
date: 2019-05-04 09:49:42
categories: 
- Spring MVC
tags: 
- Spring MVC
- 后端
---
# 第二部分 注解
## 一、分层标记
> - *@RestController* 标记控制层，在 Spring4 以后引入的复合注解，即`@Controller`和`@ResponseBody`的结合，被标记的类默认返回 json 数据，方便使用
> - *@Service* 标记业务逻辑层
>  - *@Repository* 标记持久层
>  - *@Component* 标记普通的组件，同样可以被注入到 spring 容器中管理
## 二、应用标记
> - *@SpringBootApplication* 标记启动入口类，是一个复合注解，包括`@ComponentScan`，和`@SpringBootConfiguration`，`@EnableAutoConfiguration`。
> - *@ComponentScan* 扫描当前包及其子包下被`@Component`，`@Controller`，`@Service`，`@Repository`注解标记的类并纳入到 spring 容器中进行管理。
> - *@SpringBootConfiguration*继承自`@Configuration`，二者功能也一致，标注当前类是配置类，
> - *@EnableAutoConfiguration* 启动自动的配置
## 三、地址映射
> - *@RequestMapping* 用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。
`value` 请求地址
`method` 请求方法类型
`params` 请求必须包含哪些参数才能处理
`headers` 请求必须包含`header`才能处理
`consumes` 请求内容类型`Content-Type`，例如常用的`application/json`
`produces` 指定返回的内容类型，请求头部中Accept包含该类型才能处理
> - *@GetMapping* 等于`@RequestMapping(method = RequestMethod.GET)`
> - *@PostMapping* 等于`@RequestMapping(method = RequestMethod.POST)`
> - *@PutMapping* 等于`@RequestMapping(method = RequestMethod.PUT)`
> - *@DeleteMapping* 等于`@RequestMapping(method = RequestMethod.DELETE)`
## 四、数据绑定
> - *@RequestBody* 从请求体中获取参数，一般在POST方法上传数据时使用，需要设置`Content-Type: application/json`
> - *@PathVariable* URL 路径变量，例如：`http://localhost:8080/job/delete/1`，其中的 1 便是
> - *@RequestParam* URL 问号参数，例如：`http://localhost:8080/job/delete?id=1`，其中 id 便是
> - *@ResponseBody* 将返回数据存放在响应体中
## @Controller：
>@Controller 用于标记在一个类上，使用它标记的类就是一个SpringMVC Controller 对象。分发处理器将会扫描使用了该注解的类的方法，并检测该方法是否使用了@RequestMapping 注解。
 但是单单使用@Controller 标记在一个类上还不能真正意义上的说它就SpringMVC 的一个控制器类，因为这个时候Spring 还不认识它。

这个时候有两种方式可以把MyController 交给Spring 管理，好让它能够识别我们标记的@Controller 。
 第一种方式是在SpringMVC 的配置文件中定义MyController 的bean 对象。
``` xml
<bean class="com.host.app.web.controller.MyController"/>
```
   第二种方式是在SpringMVC 的配置文件中告诉Spring 该到哪里去找标记为@Controller 的Controller 控制器。
``` xml
<context:component-scan base-package = "com.host.app.web.controller" >
       <context:exclude-filter type = "annotation" expression = "org.springframework.stereotype.Service" />
</context:component-scan > 
```
## @RequestMapping
- SpringMVC使用@RequestMapping注解为控制器指定可以处理哪些URL请求
在控制器的类定义与方法定义处都可标注
- @RequestMapping
     -类定义处：提供初步的请求映射信息。相对于WEB应用根目录
     -方法处：提供进一步的细分映射信息。相对于类定义的URL。若类定义处未标注@RequestMapping，则方法处标记的URL相对于WEB根目录
- DispatherServlet截获请求后，就通过控制器上@RequestMapping提供的映射信息确定请求所对应的处理方法
### 映射请求参数、请求方法或请求头
![映射请求参数、请求方法或请求头.png](https://upload-images.jianshu.io/upload_images/13687958-8d9999fc7d449ec0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- method 可以用来定义请求的方式
- params 和 headers 可以用来发送更加精确的映射请求。params 和 headers 支持简单的表达式
``` java
@RequestMapping(value="/testMethod",method = RequestMethod.POST)
    public String testMethod() {
        System.out.println("testMethod");
        return SUCCESS;
    }

    /*
    * 了解：可以使用 params 和 headers 来更加精确的映射请求。params 和 headers 支持简单的表达式
    * */
    @RequestMapping(value="/testParamsAndHeader",params = {"username","age!=10"},headers = {})
    public String testParamsAndHeader() {
        System.out.println("testParamsAndHeader");
        return SUCCESS;
    }
```
## @RequestMapping还支持Ant格式通配符
![Ant格式通配符.png](https://upload-images.jianshu.io/upload_images/13687958-9c718796e5a0a575.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### PathVariable
映射URL绑定的占位符
![映射URL绑定的占位符.png](https://upload-images.jianshu.io/upload_images/13687958-c5bf299c22624e58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## @PathVariable 用例：
``` java
    /*
    * @PathVariable 可以来映射URL中的占位符到目标方法的参数中
    * @param id
    * */
    @RequestMapping("/testPathVariable")
    public String testPathVariable(@PathVariable("id") Integer id) {
        System.out.println("testPathVariable: " + id);
        return SUCCESS;
    }
```
## @RequestParam
> 使用@RequestParam绑定请求参数。
> - 在处理方法入参处使用@RequestParam可以把请求参数传递给请求方法
    -value:参数名
    -required:是否必须。默认为true，表示请求参数中必须包含对应的参数，若不存在，将抛出异常
### 测试用例:
``` java
SpringMVCTest.java中添加。
/*
    * @RequestParam
    * -value:参值即请求参数的数名
    * -required:是否必须。默认为true，表示请求参数中必须包含对应的参数，若不存在，将抛出异常
    * -defaultValue：请求参数的默认值
    * */
    @RequestMapping(value = "/testRequestParam")
    public String testRequestParam(@RequestParam(value = "username") String username,
                                   // 此处 age 如果是 int 型变量，就必须设置 defaultValue="0"
                                   @RequestParam(value = "age",required = false) Integer age) {
        System.out.println("testRequestParam,uaername: " + username + " age:" + age);
        return SUCCESS;
    }
-----------------------------------------------------------------
index.jsp:
<a href="springmvc/testRequestParam?username=myName&age=19">Test Rest GET</a><br>
```
## @RequestHeader
> 绑定请求头的属性值
> - 请求头包含了若干个属性，服务器可根据此获知客户端的信息，通过@RequestHeader即可将请求头中的属性值绑定到处理方法的入参中
### 用例：
``` java
/*
    * 映射请求头信息
    * 用法同@RequestParam
    * */
    @RequestMapping(value = "/testRequestHeader")
    public String testRequestHeader(@RequestHeader(value = "User-Agent") String ua) {
        System.out.println("testRequestHeader,User-Agent: " + ua);
        return SUCCESS;
    }
------------------------------------------------------------------
index.jsp:
<a href="springmvc/testRequestHeader">testRequestHeader</a><br>
```
## @SessionAttributes
> 如果希望在多个请求之间共享某个模型属性数据(学习笔记3中会讲到)，则可以在控制类上标注一个@SessionAttributes，SpringMVC将在模型中对应的属性暂存到HttpSession中

![HttpSession.png](https://upload-images.jianshu.io/upload_images/13687958-13fc67f765b19d03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
示例：
``` java
SpringMVCTest.java 添加:

注意(***): 在 SpringMVCTest 类的上面添加：
           @SessionAttributes(value = {"user"},types = {String.class})

/*
    * @SessionAttributes除了可以通过属性名指定需要放到会话中的属性外（实际上使用的是 value 属性值），
    *                   还可以通过模型属性的对象类型指定哪些模型属性需要放到会话中（实际上使用的是 types 属性值）
    * 注意：该注解只能使用在类的上面，为不能修饰方法
    * */
    @RequestMapping("/testSessionAttributes")
    public String testSessionAttributes(Map<String,Object> map) {
        User user = new User("Tom","123456","tom@qq.com",15);
        map.put("user",user);
        map.put("school","xztywss");
        return SUCCESS;
    }
```
``` jsp
index.jsp添加：
<a href="springmvc/testSessionAttributes">Test SessionAttributes</a><br>

success.jsp添加：

    request user: ${requestScope.user }  <br><br>

    session user: ${sessionScope.user }  <br><br>

    request school: ${requestScope.school }  <br><br>

    session school: ${sessionScope.school }  <br><br>
```
## ModelAttribute
- 先上示例：
``` java
User.java添加属性（getter、setter、toString相应的改变（此处改变后不包括Address属性））:
private Integer id;    

注意：请添加一个不包含Address但是包含id的构造方法，同时应该有一个空的构造方法，不然有可能会出错。

SpringMVCTest.java添加:
/*
    * 1.有 @ModelAttribute 标记的方法会在每个目标方法被调用之前被 SpringMVC 调用！
    * 2.@ModelAttribute 注解也可以来修饰目标方法 POJO 类型的入参，其 value属性值有如下的作用：
    *   1).SpringMVC会使用 value 属性值在 implicitModel 中查找对应的对象，若存在则会直接传入到目标方法的入参中
    *   2).SpringMVC 会以 value 为 key ，POJO类型的对象为 value；存入到 request 中
    * */
    @ModelAttribute
    public void getUser(@RequestParam(value = "id",required = false) Integer id,
                        Map<String,Object> map) {
        if(id != null) {
            //模拟从数据库中获取对象
            User user = new User(1,"Tom","123456","tom@qq.com",12);
            System.out.println("从数据库中获取一个对象： " + user);

            map.put("user",user);
        }
    }

    @RequestMapping(value = "/testModelAttribute")
    public String testModelAttribute(User user) {
        System.out.println("修改： " + user);
        return SUCCESS;
    }
```
``` jsp
index.jsp 添加:
<!--
      模拟修改操作
      1.原始数据：1，Tome，123456，tom@qq.com，12
      2.密码不能被修改
      3.表单回显，模拟操作直接在表单填写对应的属性值
   -->
  <form action="springmvc/testModelAttribute" method="post">
      <input type="hidden" name="id" value="1">

      username: <input type="text" name="username" value="Tom"/>
      <br>
      email: <input type="text" name="email" value="tom@qq.com"/>
      <br>
      age: <input type="text" name="age" value="12"/>
      <br>
      <input type="submit" value="Submit">
  </form>
```
- 输出：
``` 
不包含@ModelAttribute注解的getUser()方法时的运行结果：
修改： User{id=1, username='Tom', password='null', email='tom@qq.com', age=123}

包含@ModelAttribute注解的getUser()方法时的运行结果：
从数据库中获取一个对象： User{id=1, username='Tom', password='123456', email='tom@qq.com', age=12}
修改： User{id=1, username='Tom', password='123456', email='tom@qq.com', age=13}

注：主要区别在于password回显上的区别
```
- 分析
```
/*
    * 运行流程：
    * 1.执行 @ModelAttribute 注释修饰的方法：从数据库中取出对象，把对象放入 Map 中。键为：user
    * 2.SpringMVC 从 Map 中取出 User 对象，并把表单的请求参数赋给 User 对象的对应属性。
    * 3.SpringMVC 把上述对象传入目标方法的参数
    *
    * 注意：在 @ModelAttribute 修饰的方法中，放入到 Map 时的键需要和目标方法入参类型的第一个字母小写的字符串一致
    * */
SpringMVC 确定目标方法 POJO 对象类型入参的过程 
1.确定一个 key：
    1).若目标方法的 POJO 类型的参数没有使用@ModelAttribute 作为修饰，则 key 为 POJO 类名的第一个字母小写
    2).若使用了 @ModelAttribute 注解，则 key 为 @ModelAttribute 注解的 value 值
2.在 implicitModel 中查找 key 对应的对象，若存在，则作为入参传入
    1).若在 @ModelAttribute 标记的方法中在 Map 中保存过，且 key 和 1 确定的 key 一致，则会获取到
3.若 implicitModel  中不存在 key 对应的对象，则检查当前 Handler 是否使用 @SessionAttributes 注解修饰，
若使用了该注解，且 @SessionAttributes 注解的 value 属性值中包含了 key，则会从 HttpSession中获取 key 所
对应的 value值，若存在则直接传入到目标方法的入参中。若不存在则抛出异常。
4.若 Handler 没有标识 @SessionAttributes 注解或 @SessionAttributes 注解的 value 值中不包含 key ，则会
通过反射来创建 POJO 类型的参数，传入为目标方法的参数
5.SpringMVC 会把 key 和 POJO类型的对象保存到 implicitModel 中，进而会保存到 request 中。
```
> 从上面的例子中我们可以看出，目标方法的 POJO 参数名称以及被 @ModelAttribute 标注的getUser()方法中 map 存入的 POJO 参数名称都为 POJO 参数类型名称的首字母小写。

那么，这么做是不是必须的呢？或者说我不想这么写怎么办？
解：
- 1.将被 @ModelAttribute 标注的getUser()方法中 map 存入的 POJO 参数名称改为asdf (随意修改,这里假设asdf)
- 2.为目标方法的POJO参数添加 @ModelAttribute 注解，属性 value 值为 asdf即getUser修改后为：
``` java
    @RequestMapping(value = "/testModelAttribute")
    public String testModelAttribute(@ModelAttribute("asdf")User user) {
        System.out.println("修改： " + user);
        return SUCCESS;
    }
```
## @InitBinder
![InitBinder.png](https://upload-images.jianshu.io/upload_images/13687958-f124d6ef0c242480.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
