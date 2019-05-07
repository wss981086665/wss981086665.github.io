---
title: SpringBoot学习笔记9(消息)
date: 2019-05-04 10:25:58
categories: 
- Spring Boot
tags: 
- Spring Boot
- 后端
- JavaEE
- RabbitMQ
---
## 消息
- #### 一、消息概述
![消息概述.png](https://upload-images.jianshu.io/upload_images/13687958-0745cc8671175a2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![消息概述.png](https://upload-images.jianshu.io/upload_images/13687958-32b61c54cf175479.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png.png](https://upload-images.jianshu.io/upload_images/13687958-fb651b4e0d5d0e22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- JMS与AMQP的差异
![JMS与AMQP的差异.png](https://upload-images.jianshu.io/upload_images/13687958-a7c246f50e1d160b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- #### 二、异步处理
![异步处理.png](https://upload-images.jianshu.io/upload_images/13687958-94319f6ee29c1a32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 三、RabbitMQ简介
![RabbitMQ简介.png](https://upload-images.jianshu.io/upload_images/13687958-e427d0fbdb12818e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![RabbitMQ简介.png](https://upload-images.jianshu.io/upload_images/13687958-1ec349af9ec86b2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![RabbitMQ简介.png](https://upload-images.jianshu.io/upload_images/13687958-005b9222cb787f6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 四、RabbitMQ运行机制
![RabbitMQ运行机制.png](https://upload-images.jianshu.io/upload_images/13687958-8f85e9c585d0878a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Exchange类型.png](https://upload-images.jianshu.io/upload_images/13687958-6c9c24c6e085d48e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Fanout Exchange.png](https://upload-images.jianshu.io/upload_images/13687958-3edd735916cc5e14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 五、安装与使用RabbitMQ
- Linux 安装
```
-- 下载：
docker pull rabbitmq:3-management
-- 运行：
docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq xxx
xxx 代表 IMAGE ID
注意：下载过程中涉及的镜像问题请自行百度
- p 5672端口：主机与客户端进行通信的接口
- p 15672端口：访问web管理页面的接口
```
- 测试
打开web浏览器，输入:
```
127.0.0.1:15672
也可以将127.0.0.1替换成你的服务器地址
```
会看到如下页面：输入默认的账号与密码：guest进入rabbit服务
![rabbit登录界面.png](https://upload-images.jianshu.io/upload_images/13687958-2001a2ecfa82bc40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![rabbit页面.png](https://upload-images.jianshu.io/upload_images/13687958-2bbdf1ae19406d23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 添加第一个direct类型的Exchanges
![添加第一个direct类型的Exchanges.png](https://upload-images.jianshu.io/upload_images/13687958-d45c680db154b321.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 添加消息队列
![添加消息队列.png](https://upload-images.jianshu.io/upload_images/13687958-59271094ca456c6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 为Exchanges绑定Queues
![为Exchanges绑定Queues.png](https://upload-images.jianshu.io/upload_images/13687958-9099e14be38d6962.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- exchange.topic需要模糊匹配
![exchange.topic模糊匹配.png](https://upload-images.jianshu.io/upload_images/13687958-6f27257e0074782c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- exchange发送消息
> exchange.direct 只会想与key匹配成功的Queue发送消息
> exchange.fanout会向所有的Queue发送消息
> exchange.topic会向与匹配规则相匹配的Queue发送消息

下面以exchange.direct发送消息为例
![exchange.direct页面发送消息.png](https://upload-images.jianshu.io/upload_images/13687958-163cc4f8043a42fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- Queues之helloworld获取消息
![helloworld获取消息.png](https://upload-images.jianshu.io/upload_images/13687958-9d927ecf2f0515a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 六、SpringBoot整合RabbitMQ
- 引入spring-boot-starter-amqp
``` xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```
- properties文件配置
``` yml
spring.rabbitmq.host=xxx    //xxx: rabbitmq运行的ip地址
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
#spring.rabbitmq.virtual-host
```
- 主类
``` java
/*
* 自动配置
*  1、RabbitAutoConfiguration
*  2.有自动配置的连接工厂ConnectionFactory;
*  3.RabbitProperties封装了RabbitMQ的配置
*  4、RabbitTemplate,给RabbitMQ发送和接收消息
*  5、AmqpAdmin:RabbitMQ系统管理功能组件
*  6.@EnableRabbit + @RabbitListener 监听消息队列的内容
* */

@EnableRabbit   //开启基于注解的Rabbit模式
@SpringBootApplication
public class Springboot1300AmqpApplication {

	public static void main(String[] args) {
		SpringApplication.run(Springboot1300AmqpApplication.class, args);
	}

}
```
- 首先定制序列化规则
``` java
@Configuration
public class MyAMPQConfig {
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
```
- Book对象
``` java
public class Book {

    private String bookName;

    private String author;

    public Book(){
        super();
    }

    public Book(String bookName, String author) {
        this.bookName = bookName;
        this.author = author;
    }

    public String getBookName() {
        return bookName;
    }

    public void setBookName(String bookName) {
        this.bookName = bookName;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

}
```
- 测试
``` java
@RunWith(SpringRunner.class)
@SpringBootTest
public class Springboot1300AmqpApplicationTests {

	@Autowired
	RabbitTemplate rabbitTemplate;

	/*
	* 1、单播(点对点)
	* */
	@Test
	public void contextLoads() {
//      Message需要自己构造一个;定义消息体内容和消息头
//		rabbitTemplate.send(exchange,routeKey,message);

//		object默认当成消息体，只需要传入要发送的对象,自动序列化发给rabbitmq;
//		rabbitTemplate.convertAndSend(exchange,routeKey,object);

		Map<String,Object> map = new HashMap<>();
		map.put("msg","这是第一个消息");
		map.put("data", Arrays.asList("helloworld",123,true));
		//对象被默认序列化以后发送出去
//		rabbitTemplate.convertAndSend("exchange.direct","helloworld.news",new Book("XXX","王圣嵩"));
		rabbitTemplate.convertAndSend("exchange.direct","helloworld",map);


	}

	//接收数据,如何将数据自动的转为json数据
	@Test
	public void receive(){
		Object o = rabbitTemplate.receiveAndConvert("helloworld");
		System.out.println(o.getClass());
		System.out.println(o);
	}

	/*
	* 2.广播
	* */
	@Test
	public void sendMsg(){
		rabbitTemplate.convertAndSend("exchange.fanout","",new Book("XXX","王圣嵩"));
	}
}
```
- 使用监听模式
``` java
@Service
public class BookService {

    //想要RabbitListener起作用,需要开启基于注解的Rabbit模式(主程序中标注@EnableRabbit)
    @RabbitListener(queues = "helloworld.news")
    public void receive(Book book){
        System.out.println("收到消息"+book);
    }
    
    @RabbitListener(queues = "helloworld")
    public void receive02(Message message) {
        System.out.println(message.getBody());
        System.out.println(message.getMessageProperties());
    }

}
```
- 使用SpringBoot管理Exchange与Queue
``` java
@Test
	public void creatExchange() {
		amqpAdmin.declareExchange(new DirectExchange("amqpadmin.exchange"));
		System.out.println("创建完成");

		amqpAdmin.declareQueue(new Queue("amqpadmin.queue",true));

		// 创建绑定规则
		amqpAdmin.declareBinding(new Binding("amqpadmin.queue", Binding.DestinationType.QUEUE,"amqpadmin.exchange","amqp.haha",null));
	}
```
