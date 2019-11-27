---
title: Java-基础篇-了解多线程
date: 2019-11-27 13:32:48
 - Java
categories:
 - 后端编程
---


# 1. 多线程介绍

多线程执行是Java平台的一个本质特性。每一个应用程序有至少一个线程（或者如果计算系统线程的话，如内存管理和信号处理，则是多个）。但是从应用程序程序员的角度，启动的只有一个线程，称为主线程（main thread）。这个线程有能力创建另外的线程。

[什么是进程、线程、协程](https://mrliuqh.github.io/2018/07/13/%E8%BF%9B%E7%A8%8B%E3%80%81%E7%BA%BF%E7%A8%8B%E3%80%81%E5%8D%8F%E7%A8%8B/)

# 2. 体验多线程
## 2.1 单线程代码

**代码示例:**

```java
public class ThreadDemo
{
	public static void main(String args[]){
		new TestThread().run();
		//循环输出
		for (int i=0;i<5;++i){
			System.out.println("main线程在运行");
		}
	}
}
class TestThread
{
	public void run(){
		for (int i=0;i<5;++i){
			System.out.println("TestThread在运行");
		}
	}
}

/**输出:

TestThread在运行
TestThread在运行
TestThread在运行
TestThread在运行
TestThread在运行
main线程在运行
main线程在运行
main线程在运行
main线程在运行
main线程在运行
*/
```

**代码分析:**

从上述代码中可以看出,如果要想运行main方法中的for循环,则必须要等TestThread类中的run()方法执行完后才可以运行，虽然mainain方法中for循环不依赖前面的代码块的计算结果，但是它也必须等待。这便是单一线程的缺陷。

## 2.2 多线程代码
通过继承Thread类实现多线程,Thread类存放于java.lang类库里。在Thread类中定义了run()方法，要想实现多线程，必须覆写Thread类的run方法。

**<span id="jump">代码示例</span>:**

```
public class ThreadDemo {
    public static void main(String[] args) {
        //激活一个线程
        new TestThread().start(); 
        //循环输出
        for (int i = 0; i < 5; ++i) {
            System.out.println("main线程在运行");
            try {
                //为了看出并发执行，睡眠1秒
                Thread.sleep(1000); 
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
class TestThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; ++i) {
            System.out.println("TestThread在运行");
            try {
                //为了看出并发执行，睡眠1秒
                Thread.sleep(1000); 
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
/**输出:

TestThread在运行
main线程在运行
TestThread在运行
main线程在运行
TestThread在运行
main线程在运行
TestThread在运行
main线程在运行
TestThread在运行
main线程在运行
*/
```
# 3. 使用多线程
在java中，启动一个新线程有两种途径:
- 提供一个Runnable接口的对象
- 使用Thread的子类

## 3.1 通过Runnable接口实现

**使用格式:**
Runnable接口定义了一个单一的方法run()，在run()方法中包含要在线程中执行的代码。Runnable对象被传递给Thread的构造器中，如下面的代码所示。

```
public class RunnableDemo implements Runnable {
    public void run() { 
      // todo 线程执行方法体...
    }
    public static void main(String[] args) {
      // 启动多线程
      (new Thread(new RunnableDemo())).start();
    }
}
```

**代码示例:**
```
// TestThread类实现了Runnable接口，
// 同时覆写了Runnable接口之中的run()方法，也就是说此类为一个多线程实现类
class TestThread implements Runnable {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("TestThread在运行");
            try {
                Thread.sleep(1000); //睡眠1秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

// 测试多线程
public class RunnableThread {
    public static void main(String[] args) {
        // 实例化了一个TestThread类的对象
        TestThread t = new TestThread();
        //使用Thread类的start方法启动线程
        new Thread(t).start(); 
        for (int i = 0; i < 5; i++) {
            System.out.println("main线程在运行");
            try {
                Thread.sleep(1000); //睡眠1秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 问题1: Runnable对象,为什么要调用Thread类中的start方法？

为什么实现了Runnable接口还需要调用Thread类中的start方法才能启动多线程？查找JDK文档就可以发现，<font color=red>在Runnable接口中只有一个run方法，并没有start方法。</font>

所以一个类即使实现了Runnable接口，也需用Thread类中的start方法来启动多线程。对这一点，通过查找JDK文档中的Thread类可以看到，在Thread类之中有这样一个构造方法。
```
public Thread( Runnable target )
```
由此构造方法可以看到，可以将一个Runnable接口（或其子类）的实例化对象作为参数去实例化Thread类对象。


## 3.2 通过Thread的子类实现

**使用格式:**
```
public class ThreadDemo extends Thread {
    //重写从Thread继承过来的run方法
    public void run() { 
        // todo 线程执行方法体..
    }
    public static void main(String[] args) {
        // 启动多线程
        (new Thread(new ThreadDemo())).start();
    }
}
```
[代码示例见:多线程代码](#jump)


> Runnable接口和Thread类的关系:Thread类是Runnable接口的一个子类


# 4. 深入案例-卖火车票

下面是模拟一个铁路售票系统的范例，某日列车的车票剩余5张 ，通过4个售票点来售卖，一个售票点用一个线程来表示

## 4.1 Thread子类实现
*Thread子类实现-代码示例:*
```java
public class ThreadDemo {
    public static void main(String[] args) {
        TestThread t = new TestThread();
        //一个线程对象只能启动一次
        t.start();
        t.start();
        t.start();
        t.start();
    }
}
public class TestThread extends Thread {
    private int tickets = 5;
    public void run() {
        while (tickets > 0) {
            // 获取当前运行线程的名称
            Sting ThreadName = Thread.currentThread().getName();
            System.out.println(ThreadName + "出售票" + tickets);
            tickets -= 1;
        }
    }
}

/***
* 输出:
Thread-0出售票5
Thread-0出售票4
Thread-0出售票3
Thread-0出售票2
Thread-0出售票1
Exception in thread "main" java.lang.IllegalThreadStateException
	at java.lang.Thread.start(Thread.java:708)
	at Main.main(Main.java:15)
*/
```
**<font color=fc7900>@注意:</font> 一个类继承Thread类之后，这个类的实例化对象，无论调用多少次start方法，结果都只有一个线程在运行**


*Thread子类实现-代码修改:*
```java
public class TestThreadDemo {
    public static void main(String[] args) {
        TestThread t = new TestThread();
        //启动4个线程
        new TreadDemo().start();
        new TreadDemo().start();
        new TreadDemo().start();
        new TreadDemo().start();
    }
}
public class TestThread extends Thread {
    // 一共剩余5张票
    private int tickets = 5;
    public void run() {
        while (tickets > 0) {
            // 获取当前运行线程的名称
            Sting ThreadName = Thread.currentThread().getName();
            System.out.println(ThreadName + "出售票" + tickets);
            tickets -= 1;
        }
    }
}

/***
*输出:
Thread-0出售票5
Thread-1出售票5
Thread-0出售票4
Thread-1出售票4
Thread-1出售票3
Thread-1出售票2
Thread-1出售票1
Thread-2出售票5
Thread-0出售票3
Thread-2出售票4
Thread-2出售票3
Thread-2出售票2
Thread-2出售票1
Thread-0出售票2
Thread-0出售票1
Thread-3出售票5
Thread-3出售票4
Thread-3出售票3
Thread-3出售票2
Thread-3出售票1
*/
```


**问题1:剩余票一共5张，却卖出了4\*5 = 20张**

我们的本意是一共有5张票，每个线程模拟一个售票窗口，一起把这5张票卖完，但从运行的结果可以看出，每个线程都卖了5张票，这样就卖出了4×5=20张票，这不是我们所需要的。因此，<font color=red>用Thread实际上很难达到资源共享的目的，但是可以通过静态变量达到资源共享，</font>例如，可将tickets设置为static类型的。

*修改方法:改成静态变量*
```
// 一共剩余5张票
private static int tickets = 5;

```
> <font color=red>即便改成静态变量也会出现一票多卖的情况,原因下稍后说明。</font>

## 4.2 Runnabe接口实现
*Runnabe接口实现(共享资源)-代码示例:*

```
public class RunnableDemo implements Runnable {
    /**
     * description: 剩余总票数
     */
    private static int tickets = 5;

    @Override
    public void run() {
        while (tickets > 0) {
            // 获取当前运行线程的名称
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName + "出售票" + tickets);
            tickets -= 1;
        }
    }
}

public class TestThreadDemo {
    public static void main(String[] args) {
        RunnableDemo r = new RunnableDemo();
        //启动4个线程
        new Thread(r).start();
        new Thread(r).start();
        new Thread(r).start();
        new Thread(r).start();
    }
}

/***
*输出:
Thread-0出售票5
Thread-3出售票5
Thread-2出售票5
Thread-1出售票4
Thread-0出售票3
Thread-1出售票2
Thread-1出售票0
*/
```

<div style="color:green">
实现Runnable接口相对于继承Thread类来说，有如下几个显著的优势。

1. 避免由于Java的单继承特性带来的局限。
2. 可以使多个线程共享相同的资源，以达到资源共享的目的。

</div>


## 4.3 一票多卖
不管是Thread子类实现，还是Runnabe接口实现的程序，多运行几次本程序，就会发现一票多卖的情况。比如在上面的运行结果中,第5张票就被线程0、线程2和线程3卖了3次，出现“一票多卖”的现象。

<font color=orange>这是因为:当tickets=1时，线程0、线程2和线程3都同时看见了，满足条件tickets > 0，当第一个线程就把票卖出去了，tickets理应减1，当它还没有来得及更新，当前的线程的运行时间片就到了，必须推出CPU，让其他线程执行，而其他线程看到的tickets依然是旧状态（tickets=1），所以，依次也把那张已经卖出去的票再次“卖”出去了。</font>

事实上，在多线程运行环境中，tickets属于典型的临界资源（Critical resource），而run方法体就属于临界区（Critical Section）。多个进程中涉及到同一个临界资源的临界区称为相关临界区。


# 5. 线程状态
每个Java程序都有一个默认的主线程，对于Java应用程序，主线程是main方法执行的线程；要想实现多线程，必须在主线程中创建新的线程对象。而一个线程具有5种状态，即创建、就绪、运行、阻塞、终止。

**线程状态的转移与转移原因之间的关系如下图所示:**
![](https://mrliuqh.github.io/directionsImg/java/threadStatus.png)

在给定时间点上，一个线程只能处于一种状态（[详见JDK文档 Thread.State](https://www.tbaqkj.com/javase/8/docs/api/)）
1. New(创建态): 至今尚未启动的线程处于这种状态。
2. Runnable(运行态):正在Java虚拟机中执行的线程处于这种状态。
3. Blocked(阻塞态):受阻塞并等待某个监视器锁的线程处于这种状态。
4. Waiting(无限等待态):无限期的等待另一个线程来执行某一个特定操作的线程处于这种状态。
5. Timed_Waiting(限时等待态):具有指定等待时间的某一等待线程的线程状态。
6. Terminated(死亡态):已退出的线程处于这种状态。


## 5.1 代码演示线程生命周期 
```
package com.training.thread;

import java.util.Scanner;

/**
 * description :演示线程生命周期 
 * @author : Mr.Liuqh
 * @date : 2019-11-27 13:12
 */

public class ThreadLife implements Runnable {
    @Override
    public void run() {
        System.out.println("处于运行状态!");
        Scanner scanner = new Scanner(System.in);
        System.out.println("等待I/O,处于阻塞状态!");
        System.out.println("请输入字符串：");
        //next()方法扫描scanner输入的字符串
        scanner.next();
        //扫描器结束，系统不再等待I/O，线程重新进入就绪状态
        scanner.close();
        System.out.println("结束阻塞状态,重新进入就绪状态,然后运行状态!");
        try {
            //线程暂停1000毫秒
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("线程进入死亡状态!");
    }

    public static void main(String[] args) {
        Thread thread = new Thread(new ThreadLife());
        System.out.println("处于创建状态!");
        thread.start();
        System.out.println("处于就绪状态!");
    }
}
```
输出:
```
处于创建状态!
处于就绪状态!
处于运行状态!
等待I/O,处于阻塞状态!
请输入字符串：
hello
结束阻塞状态,重新进入就绪状态,然后运行状态!
线程进入死亡状态!
```

# 6. 操作线程的方法
操作线程的主要方法在Thread类中，下表列出了Thread类中的主要方法，[查阅SDK文档获得更多线程方法](https://www.tbaqkj.com/javase/8/docs/api/)。

![](https://mrliuqh.github.io/directionsImg/java/thread-fun-1.png)
![](https://mrliuqh.github.io/directionsImg/java/thread-fun-2.png)

