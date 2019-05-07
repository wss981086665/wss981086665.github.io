---
title: Java高并发编程
date: 2019-05-04 10:07:07
categories: 
- Java高并发
tags: 
- Java高并发
- JavaEE
---
> **注：其一、本文章为作者读完《实战Java高并发程序设计》之后所总结的知识，其中涵盖了每一章节的精髓之处。其二、文章中一部分代码直接引自书中。**

# 一、并行基础
## 1.1线程的基本操作
### 1.1.1.终止线程
- Thread.stop(): 在结束线程时会直接终止线程

>  在不清楚的情况下不可随便使用，因为强行终止可能会损坏文件中的数据
### 1.1.2.线程中断
- static native void sleep(long millis) throws InterruptedException  (暂先了解):
> `Thraed.sleep()方法`会让当前线程休眠若干时间，它会抛出一个`InterruptedException`中断异常。但是`InterruptedException` 不是运行时异常，也就是说程序必须主动捕获并处理它。线程在`sleep()`休眠时，如果被中断，这个异常就会产生。  
- void Thread.interrupt(): 一个实例方法,通知目标线程中断，会设置一个中断标志    
>  可与中断判断方法搭配来实现中断线程的目的(例如:如果判断方法返回数据为中断状态就退出循环终止线程)
- boolean Thraed.isInterrupted(): 用于判断当前线程是否被中断
- static boolean Thread.interrupted(): 判断当前线程是否被中断，并清除当前中断状态
### 1.1.3.等待(wait)和通知(notify)

- final void wait() throws InterruptedException //
- final native void notify()
- final native void notifyAll()
-        

>  这两个方法不属于`Thraed类`，而是属于`Object类`。这两个方法在执行之前都需要先获得相应对象的锁，`wait()方法`执行之后会释放这个锁，但是`notify()`不会立刻立刻释放`sycronized(object)`中的`object锁`，必须要等`notify()`所在线程执行完`synchronized(object)`块中的所有代码才会释放这把锁。当一个线程调用了`object.wait()`之后，它会进入该对象的等待队列，该队列中可能会有多个等待同一对象的线程。当对象调用`object.notify()`的时候，它会从等待队列中随机唤醒一个线程(这是不公平的)。线程被释放之后不会立即执行，而是尝试获取对象的锁，如果获取失败，还需要先等待对象的锁。`notifyAll()`会唤醒队列中所有的线程

### 1.1.4.挂起(suspend)和继续执行(resume)线程
在JDK文档中这两个方法已经被标注为废弃方法
- suspend(): 方法在导致线程暂停的同时不会释放任何锁资源，直到对应的线程执行了resume()方法才能使被挂起的线程继续执行

> `suspend()`所占用的锁不会被释放，因此可能导致整个系统工作不正常。有时resume()方法写在suspend(0方法之后，但是由于时间先后顺序的缘故，`resume()`就可能无法生效，这会导致线程被永远挂起，并且一直占用对象的锁，这对于系统来说是非常致命的

### 1.1.5.等待线程结束(join)和谦让(yield)
- final void join() throws InterruptedException
- final synchronized void join(long millis) throws InterruptedException

> `第一个join()方法`会无限等待，它会一直阻塞当前线程，直到目标线程运行结束。`第二个join()方法`给出了等待时间，如果超过了等待时间线程还在继续执行，当前线程就会继续执行。

- static native void yield()

> yield()方法执行之后会是当前线程释放CPU，释放CPU之后会重新争夺CPU资源的争夺，但不一定能分配到资源

## 1.2.volatile
为变量添加关键字volatile声明之后，保证了该变量的可见性，应用程序范围内的所有线程都能“看到”这个改动。但是，volatile不能保证一些复合操作的原子性，例如:

``` java
static volatile int i=0;
	public static class VolatileDemo Runnable{
		@Override
		public void run() {
			for(int k=0;k<10000;k++)
				i++;
		}
	}
	public static void main(String[] args) throws InterruptedException {
		Thread[] threads=new Thread[10];
		for(int i=0;i<10;i++){
			threads[i]=new Thread(new VolatileDemo());
			threads[i].start();
		}
		for(int i=0;i<10;i++){
			threads[i].join();
		}
		
		System.out.println(i);
	}
```
    

> 按我们的猜测，这段代码执行之后应该得到的结果应该为100000，但是得到的结果总是小于100000，课件volatile并不能保证复合操作的原子性

## 1.3.线程组
- ThreadGroup tg = new ThreadGroup("PrintGroup");
-     

> 线程组的优点就是更方便管理一对数组.在线程创建的时候为线程添加第一个参数为线程组的名称，第三个参数为现成的名称即可
>例如:`Thraed t1 = new Thraed(tg,new RunnableDemo(),"T1");`
>线程组中有一个值得注意的方法：stop()，它会停止线程组中所有的线程，但使用时同样需要谨慎

## 1.4.守护线程(Daemon)
- thread.setDaemo(true);

> 设置守护线程必须在线程start()之前设置，否则会抛出异常:`java.lang.IllegalThreadStateException`。守护线程会随着main函数结束而结束，会在main函数休眠2秒后退出
## 1.5.关键字synchronized
**synchronized的三种用法:**
- 指定加锁对象：给给定对象加锁，进入同步代码块前要获得给定对象的锁
- 直接作用于实例方法：相当于对当前实例进行加锁，进入同步代码块前要获得当前实例的锁
- 直接作用于静态方法：相当于对当前类进行加锁，进入同步代码块前要获得当前类的锁
> 作用于指定对象与作用于实例方法都需要注意：加锁的对象一定要是同一个对象，否则代码块就有可能无法正确执行。如果是作用于静态方法，那么即使两个线程指向的不是同一个对象，也能保证程序正确执行
以下两个代码块所产生的效果是相同的(代码引用自《实战Java高并发程序设计》)
``` java
public class AccountingSync2 implements Runnable{
	static AccountingSync2 instance=new AccountingSync2();
	static int i=0;
	public synchronized void increase(){
		i++;
	}
	@Override
	public void run() {
		for(int j=0;j<10000000;j++){
			increase();
		}
	}
	public static void main(String[] args) throws InterruptedException {
	
		***Thread t1=new Thread(instance);
		Thread t2=new Thread(instance);***
		
		t1.start();t2.start();
		t1.join();t2.join();
		System.out.println(i);
	}
}
```
``` java
public class AccountingSyncBad implements Runnable{
	static int i=0;
	public synchronized void increase(){
		i++;
	}
	@Override
	public void run() {
		for(int j=0;j<10000000;j++){
			increase();
		}
	}
	public static void main(String[] args) throws InterruptedException {
	
		***Thread t1=new Thread(new AccountingSyncBad());
		Thread t2=new Thread(new AccountingSyncBad());***
		
		t1.start();t2.start();
		t1.join();t2.join();
		System.out.println(i);
	}
}
```
# 二、JDK并发包
## 2.1.同步控制
### 2.1.1.synchronized的功能扩展-重入锁
重入锁使用`java.util.concurrent.locks.ReentrantLock`类来实现，简单使用案例(部分):
``` java
    public class ReenterLock implements Runnable{
    	public static ReentrantLock lock=new ReentrantLock();
    	public static int i=0;
    	@Override
    	public void run() {
    		for(int j=0;j<10000000;j++){
    			lock.lock();
    			try{
    				i++;
    			}finally{
    				lock.unlock();
    			}
    		}
    	}
    	public static void main(String[] args) throws InterruptedException {
    	
    		***ReenterLock tl=new ReenterLock();
    		Thread t1=new Thread(tl);
    		Thread t2=new Thread(tl);***
   
    		t1.start();t2.start();
    		t1.join();t2.join();
    		System.out.println(i);
    	}
    }
```
> 重入锁，顾名思义，此锁可反复进入，但是一个线程获得几次锁，就要释放锁时也要释放相同的次数，释放次数多了会抛出异常:`java.lang.IllegalMonitorStateException`

``` java
	lock.lock();
	lock.lock();
	try{
		i++;
	}finally{
		lock.unlock();
		lock.unlock();
	}
```
#### 2.1.1.1.中断响应
中断响应处理死锁问题：如果程序中所有获取锁的方式均使用可以对中断作出响应的方法获取锁，那么就可以在主线程main睡眠的时候中断一个线程，该线程就会放弃对锁的申请
#### 2.1.1.2.锁申请等待限时
**锁申请等待限时** 为避免死锁的另一种处理方式(重入锁ReentrantLock实现)

``` java
public class TimeLock implements Runnable{
	public static ReentrantLock lock=new ReentrantLock();
	@Override
	public void run() {
		try {
			if(lock.tryLock(5, TimeUnit.SECONDS)){
				Thread.sleep(6000);
			}else{
				System.out.println("get lock failed");
			}
		} catch (InterruptedException e) {
			e.printStackTrace();
		}finally{lock.unlock();}
	}
	public static void main(String[] args) {
		TimeLock tl=new TimeLock();
		Thread t1=new Thread(tl);
		Thread t2=new Thread(tl);
		t1.start();
		t2.start();
	}
}
```

> 在这里，tryLock()方法有两个参数，第一个参数为等待时长，第二个参数为计时单位，这里两个参数表示的意思是5秒内尝试获得锁，如果超过5秒还未获取到锁，就返回false，获取成功返回true

> ReentrantLock.tryLock()方法也可以不设置参数：当前线程会尝试获得锁，获取成功返回true，如果锁正在被其他线程占用，当前线程也不会等待，而是直接返回true
#### 2.1.1.3.公平锁                                                              
重入锁允许我们对其公平性进行设置

``` java
public ReentrantLock(boolean fair)
```
当参数fair为true时，表示锁是公平的。公平锁体现在：为线程按请求按顺序分配锁。但是实现公锁需要维护一个有序队列，因此公平锁的实现成本比较高。如果没有特殊要求则不需要使用公平锁  
#### 2.1.1.4.ReentrantLock的几个重要方法
- lock(): 获得锁，如果所已经被占用则等待
- lockInterruptibly(): 获得锁，但优先响应中断
- tryLock(): 尝试获得锁，如果成功，则返回true，失败返回false。该方法不等待，立即返回
- tryLock(long time,TimeUnit unit): 在给定时间内尝试获得锁
- unlock(): 释放锁
### 2.1.2.Condition

> `Condition与重入锁相关联的`( wait()与notify()方法是与synchronized关键字搭配使用 )

**创建: 通过locak接口(重入锁实现了这一接口)的Condition newCondition()方法可以生成一个与当前重入锁绑定的Condition实例**
**Condition接口提供的基本方法如下:**
``` java
- void await() throws InterruptedException;
- void awaitUninterruptibly();
- void awaitNanos(long nanosTimeout) throws InterruptedException;
- boolean await(long time,TimeUnit unit) throws InterruptedException;
- boolean awaitUntil(Date deadline) throws InterruptedException;
- void signal();
- void signalAll();
```
> 使用`Condition.await()`方法时，要求线程持有相关的重入锁，在`Condition.await()`方法调用之后，这个线程会释放这把锁。`signal()`方法执行后，系统会从当前Condition对象的等待队列中唤醒一个线程。在signal()方法执行后一般要释放相关的锁，让给被唤醒的线程
### 2.1.3.信号量(Semaphore)
信号量为多线程协作提供了更为强大的控制方法。广义上说，信号量是对锁的扩展
**构造函数:**
``` java
public Semaphore(int permits)                   //permits为准入数
public Semaphore(int permits,boolean fair)      //第二个参数可以指定是否公平
```
**信号量的主要逻辑方法:**
``` java
public void acquire()                
public void acquireUninterruptibly()
public boolean tryAcquire()
public boolean tryAcquire(long timeout,TimeUnit unit)
public void release()
```
> `acquire()`尝试或得一个准入的许可，线程如果没有获得就等待，直到有线程释放一个许可或当前线程被中断。
> `acquireUninterruptibly()`与 `acquire()`类似，但是不响应中断
> `tryAcquire()`尝试获得一个许可，成功返回true，失败返回false，它不会进行等待
> `release()`用于在线程访问资源结束后释放一个许可。

简单的例子:
``` java
public class SemapDemo implements Runnable {
	final Semaphore semp = new Semaphore(5);

	@Override
	public void run() {
		try {
			semp.acquire();
			Thread.sleep(2000);
			System.out.println(Thread.currentThread().getId() + ":done!");
		} catch (InterruptedException e) {
			e.printStackTrace();
		} finally {
			semp.release();
		}
	}

	public static void main(String[] args) {
		ExecutorService exec = Executors.newFixedThreadPool(20);
		final SemapDemo demo = new SemapDemo();
		for (int i = 0; i < 20; i++) {
			exec.submit(demo);
		}
	}
}
```
### 2.1.4.ReadWriteLock读写锁
读写分离锁：读写分离所可以有效地帮助减少锁竞争，提升系统性能。
通过重入读写锁分别生成读锁和写锁，减少没必要的竞争，提高运行效率

|  | 读 | 写 |
|--|--|--|
| 读 | 非阻塞 | 阻塞 |
| 写 | 阻塞 | 阻塞 |
在下面的例子中，读线程完全并行，而写会阻塞读
如果用注释掉的代码分别替换前一行代码，所有的读和写线程之间都必须互相等待，效率将会大大降低
``` java
private static Lock lock=new ReentrantLock();
	private static ReentrantReadWriteLock readWriteLock=new ReentrantReadWriteLock();
	private static Lock readLock = readWriteLock.readLock();
	private static Lock writeLock = readWriteLock.writeLock();
	private int value;
	
	public Object handleRead(Lock lock) throws InterruptedException{
		try{
			lock.lock();				//模拟读操作
			Thread.sleep(1000);			//读操作的耗时越多，读写锁的优势就越明显
			return value;				
		}finally{
		lock.unlock();
		}
	}

	public void handleWrite(Lock lock,int index) throws InterruptedException{
		try{
			lock.lock();				//模拟写操作
			Thread.sleep(1000);
			value=index;
		}finally{
		lock.unlock();
		}
	}
	
	public static void main(String[] args) {
		final ReadWriteLockDemo demo=new ReadWriteLockDemo();
		Runnable readRunnale=new Runnable() {
			@Override
			public void run() {
				try {
//					demo.handleRead(readLock);
					demo.handleRead(lock);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		};
		Runnable writeRunnale=new Runnable() {
			@Override
			public void run() {
				try {
//					demo.handleWrite(writeLock,new Random().nextInt());
					demo.handleWrite(lock,new Random().nextInt());
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
		};
       
        for(int i=0;i<18;i++){
            new Thread(readRunnale).start();
        }
        
        for(int i=18;i<20;i++){
            new Thread(writeRunnale).start();
        }	
	}
```
### 2.1.5.倒计时器:CountDownLatch
当使用计数器计时的时候，每次执行完一个线程就执行CountDownLatch.countDown()使计数count-1，直到count为0的时候解除CountDownLatch.await()对主线程main的阻塞
**构造函数：**
``` java
public CountDownLatch(int count)
```
**示例代码:**
``` java
/**
 * 倒数计时器
 * @author Geym
 *
 */
public class CountDownLatchDemo implements Runnable {
    static final CountDownLatch end = new CountDownLatch(10);
    static final CountDownLatchDemo demo=new CountDownLatchDemo();
    @Override
    public void run() {
        try {
            //模拟检查任务
            Thread.sleep(new Random().nextInt(10)*1000);
            System.out.println("check complete");
            end.countDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) throws InterruptedException {
        ExecutorService exec = Executors.newFixedThreadPool(10);
        for(int i=0;i<10;i++){
            exec.submit(demo);
        }
        //等待检查
        end.await();
        //发射火箭
        System.out.println("Fire!");
        exec.shutdown();
    }
}
```
### 2.1.6.循环栅栏：CyclicBarrier
**CyclicBarrier是一种多线程并发控制工具**

构造函数:
``` java
public CyclicBarrier(int parties,Runnable barrierAction)
* int parties: 表示计数总数
* Runnable  barrierAction: 当计数达到parties之后系统所要执行的动作。
* barrierAction在线程集合完毕时执行一次，所有线程执行完毕会再次执行一次
```

> 大概意思是栅栏有一个计时器，当有一个线程请求执行时，栅栏计数器+1，当到达指定n后，循环栅栏一次执行这n个线程。然后如此循环进行

案例: 司令下达命令，要求10个士兵一起去完成一项任务。这时就会要求10个士兵先集合报道，然后一起去执行任务。当10个士兵全部都完成任务后，司令才能宣布任务完成
**代码实现：**
``` java
public class CyclicBarrierDemo {
    public static class Soldier implements Runnable {
        private String soldier;
        private final CyclicBarrier cyclic;

        Soldier(CyclicBarrier cyclic, String soldierName) {
            this.cyclic = cyclic;
            this.soldier = soldierName;
        }

        public void run() {
            try {
                //等待所有士兵到齐
                cyclic.await();
                doWork();
                //等待所有士兵完成工作
                cyclic.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }

        void doWork() {
            try {
                Thread.sleep(Math.abs(new Random().nextInt()%10000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(soldier + ":任务完成");
        }
    }

    public static class BarrierRun implements Runnable {
        boolean flag;
        int N;
        public BarrierRun(boolean flag, int N) {
            this.flag = flag;
            this.N = N;
        }

        public void run() {
            if (flag) {
                System.out.println("司令:[士兵" + N + "个，任务完成！]");
            } else {
                System.out.println("司令:[士兵" + N + "个，集合完毕！]");
                flag = true;
            }
        }
    }

    public static void main(String args[]) throws InterruptedException {
        final int N = 10;
        Thread[] allSoldier=new Thread[N];
        boolean flag = false;
        CyclicBarrier cyclic = new CyclicBarrier(N, new BarrierRun(flag, N));
        //设置屏障点，主要是为了执行这个方法
        System.out.println("集合队伍！");
        for (int i = 0; i < N; ++i) {
            System.out.println("士兵 "+i+" 报道！");
            allSoldier[i]=new Thread(new Soldier(cyclic, "士兵 " + i));
            allSoldier[i].start();
        }
    }
}

```
**输出结果:**
```
集合队伍！
士兵 0 报道！
//篇幅有限，省略其他几个士兵
士兵 9 报道！
司令：[士兵10个，集合完毕！]
士兵0：任务完成
//篇幅有限，省略其他几个士兵
士兵9：任务完成
司令：[士兵10个，任务完成]
```

> `CyclicBarrier.await()`方法可能会抛出两个异常，第一个是中断异常，这里不多做描述。第二个异常是`BrokenBarrierException`，遇到这个异常，则表示当前的CyclicBarrier已经破损

### 2.1.7.线程阻塞工具类：LockSupport

> `LockSupport`可以在线程内任意位置让线程阻塞。与`Thread.suspend()`方法相比，它弥补了由于`resume()`方法发生导致的线程无法继续执行的情况。与`Object.wait()`相比，他不需要先获得某个对象的锁。也不会抛出`InterruptedException`异常
> ·
> LockSupport的静态方法park()可以阻塞当前线程，类似的还有parkNanos()、parkUntil()等方法。LockSupport的静态方法unpark()可以让线程继续执行。代码书写正确了，即使无法保证unpark()会发生在park()之前，程序也会正确的执行。即使unpark()发生在park()之前，它也可以使下一次的park()方法操作立即返回。因为LockSupport类使用类似信号量的机制。
> ·
> LockSupport.park()方法还能支持中断影响，但它不会抛出InterruptedException异常，它只是默默返回，但是可以从Thread.interrupted()等方法中获得中断标记
### 3.1.8.Guava和RateLimiter
> Guava是Google的一个核心库，RateLimiter是Guava的一款限流工具
> ·
> 漏桶算法：利用一个缓冲区，当有请求进入系统时，无论请求的速率如何，都现在缓存区内保存，然后以固定的流速流出缓存区进行处理。**特点：无论外界请求压力如何，漏桶算法总是以固定的流速处理数据。漏桶的容积和流出速率是该算法的两个重要参数
> ·
> 令牌桶算法：一种反向的漏桶算法。在令牌桶算法中，桶中存放的不再是请求，而是令牌。处理程序只有拿到令牌后，才能对请求进行处理。如果没有令牌，那么处理程序要么丢弃请求，要么等待可用的令牌。为了限制流速，该算法在每个单位时间内产生一定量的令牌放入桶中

**RateLimiter正是采用了令牌桶算法。下列是RateLimiter的使用方法:**
``` java
public class RateLimiterDemo {
	static RateLimiter limiter = RateLimiter.create(2);

	public static class Task implements Runnable {
		@Override
		public void run() {
			System.out.println(System.currentTimeMillis());
		}
	}

	public static void main(String args[]) throws InterruptedException {
		for (int i = 0; i < 50; i++) {
			limiter.acquire();
			new Thread(new Task()).start();
		}
	}
}
```
**输出如下：**

``` java
1527947609270
1527947609768
1527947600268
1527947600768      //ms时间      每秒输出两个结果
```

> 但是在一些系统无法处理请求的场景中，为了保证服务质量，更倾向于直接丢弃过载请求(`tryAcquire()`)
## 3.2.线程池
## 线程池将在后续更新中总结...
![奋斗的小熊猫](http://upload-images.jianshu.io/upload_images/13687958-eda392d66e9c320e.gif?imageMogr2/auto-orient/strip)