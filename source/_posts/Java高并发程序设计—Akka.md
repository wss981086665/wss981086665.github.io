---
title: Java高并发程序设计—Akka
date: 2019-05-04 10:11:19
categories: 
- Java高并发
tags: 
- Java高并发
- JavaEE
---
> **注：其一、本文章为作者读完《实战Java高并发程序设计》之后所总结的知识，其中涵盖了每一章节的精髓之处。其二、文章中一部分代码直接引自书中。**
# 使用Akka构建高并发程序设计
## 1.1.新并发模型：Actor
> 在并发程序中，线程始终是并发程序的基本执行单元。但是在`Akka`中，你将会使用一个全新的执行单元—`Actor`，而不再需要线程的概念。

在Actor模型中，我们不在需要调用对象（例如Actor）的方法去执行一些业务逻辑代码，而是通过给Actor发送一条消息。Actor接收到消息之后，它会根据消息的内容做出一些行为，包括改变自身的状态（自发式进行）

## 1.2.Akka之HelloWorld
第一个Actor实现
``` java
public class Greeter extends UntypedActor {

    public static enum Msg{
        GERRT, DONE;                   //消息类型
    }

    @Override
    public void onReceive(Object msg){
        if(msg == Msg.GERRT){
            System.out.println("Hello World!");
            getSender().tell(Msg.DONE, getSelf());
        } else {
            unhandled(msg);
        }
    }

}

```
以上代码定义了一个欢迎者(Greeter)Actor，它继承自UntypedActor（Akka的核心成员）。
* UntypedActor：为无类型的Actor，继承UntypedActor之后就不能再继承系统中其他类型的构造
* 有类型的Actor：可以使用系统中其他类型的构造，从而缓解Java单继承的问题

与Actor交流的另外一个Actor是HelloWorld，他的实现如下:
``` java
public class HelloWorld extends UntypedActor {

    ActorRef greeter;

    //Akka的回调方法，在Actor启动前由Akka调用完成一些初始化操作
    @Override
    public void preStart() {            
        //创建Greeter实例
        greeter = getContext().actorOf(Props.create(Greeter.class),"greeter");      
        System.out.println("Greeter Actor Path:" + greeter.path());
        //向Greeter发送GREET信息
        greeter.tell(Greeter.Msg.GERRT,getSelf());                 
    }

    //onReceive()函数为HelloWorld的消息处理函数。这里只处理了DONE消息，然后向Greeter发送了一条GREET信息
    //因此，Greeter会收到前后两条GREET消息，打印两次Hello World“”
    @Override
    public void onReceive(Object msg) throws Exception {           
        if(msg == Greeter.Msg.DONE) {
            greeter.tell(Greeter.Msg.GERRT, getSelf());
            getContext().stop(getSelf());
        } else {
            unhandled(msg);
        }
    }

}
```
主函数main():
``` java
public class HelloMainSimple {

    public static  void main(String[] args) {
        ActorSystem system = ActorSystem.create("Hello", ConfigFactory.load("samplehello.conf"));
        ActorRef a = system.actorOf(Props.create(HelloWorld.class),"helloworld");
        System.out.println("HelloWorld Actor Path:" + a.path());
    }

}
```
- ActorSystem : 表示管理和维护Actor的系统，一般一个应用只需要一个ActorSystem 就够了。
- ActorSystem.creat("Hello", ConfigFactory.load("samplehello.conf"))：第一个参数表示系统名称，第二个参数为配置文件。此处samplehello.conf文件的内容为：
```
akka {
    loglevel = INFO
}
```
## 1.?.消息路由
``` java
public class WatchActor extends UntypedActor {

    private final LoggingAdapter log = Logging.getLogger(getContext().system(), this);

    public Router router;
    {
        List<Routee> routees = new ArrayList<Routee>();
        for(int i=0; i<5; i++) {
            ActorRef worker = getContext().actorOf(Props.create(MyWorker.class),"worker_"+i);
            getContext().watch(worker);
            routees.add(new ActorRefRoutee(worker));
        }
        router = new Router(new RoundRobinRoutingLogic(),routees);
    }

    @Override
    public void onReceive(Object msg) {
        if(msg instanceof MyWorker.Msg) {
            router.route(msg, getSender());
        } else if (msg instanceof Terminated) {
            router = router.removeRoutee(((Terminated) msg).actor());
            System.out.println(((Terminated) msg).actor().path() + " is closed,routees=" + router.routees().size());
            if(router.routees().size() == 0) {
                System.out.println("Close system");
                RouteMain.flag.send(false);
                getContext().system().shutdown();
            }
        } else {
            unhandled(msg);
        }
    }

}
```
``` java
public class RouteMain {

    public static Agent<Boolean> flag = Agent.create(true, ExecutionContexts.global());

    public static void main(String[] args) throws InterruptedException {
        ActorSystem system = ActorSystem.create("route", ConfigFactory.load("samplehello.conf"));
        ActorRef w = system.actorOf(Props.create(WatchActor.class), "watcher");
        int i = 1;
        while(flag.get()) {
            w.tell(MyWorker.Msg.CLOSE, ActorRef.noSender());
            i++;
            Thread.sleep(100);
        }
    }

}
```
