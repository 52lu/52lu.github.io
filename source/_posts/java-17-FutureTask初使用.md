---
title: Java-进阶篇-FutureTask初使用
date: 2019-12-20 12:31:27
tags: 
 - Java
categories:
 - 后端编程
---

# 1. FutureTask简介
FutureTask除了实现Future接口外，还实现了Runnable接口。因此，FutureTask可以交给Executor执行，也可以由调用线程直接执行（FutureTask.run()）。

根据FutureTask.run()方法被执行的时机，FutureTask可以处于下面3种状态。

1. 未启动。FutureTask.run()方法还没有被执行之前，FutureTask处于未启动状态。当创建一个FutureTask，且没有执行FutureTask.run()方法之前，这个FutureTask处于未启动状态。

2. 已启动。FutureTask.run()方法被执行的过程中，FutureTask处于已启动状态。

3. 已完成。FutureTask.run()方法执行完后正常结束，或被取消（FutureTask.cancel（…）），或执行FutureTask.run()方法时抛出异常而异常结束，FutureTask处于已完成状态。

# 2. FutureTask状态迁移
![](https://52lu.github.io/images/futureTask-status-move.png)


## 2.1 get和cancel使用介绍

![](https://52lu.github.io/images/java/futureTask-get-cancel.png)
- 当FutureTask处于未启动或已启动状态时，执行FutureTask.get()方法将导致调用线程阻塞；
- 当FutureTask处于已完成状态时，执行FutureTask.get()方法将导致调用线程立即返回结果或抛出异常。
- 当FutureTask处于未启动状态时，执行FutureTask.cancel()方法将导致此任务永远不会被执行；
- 当FutureTask处于已启动状态时，执行FutureTask.cancel（true）方法将以中断执行此任务线程的方式来试图停止任务；
- 当FutureTask处于已启动状态时，执行FutureTask. cancel（false）方法将不会对正在执行此任务的线程产生影响（让正在执行的任务运行完成）；
- 当FutureTask处于已完成状态时，执行FutureTask.cancel（…）方法将返回false。


> 以上内容摘自《Java并发的艺术》第十章第四节 FutureTask详情 片段。



# 3. FutureTask使用

## 3.1 计算1~10阶乘的和
`10! + 9! + ...+1! = ?`

**阶乘的概念:**
![](https://52lu.github.io/images/math/factorial.png)


## 3.2 futureTask实现
```
package com.hui.javalearn.thread.futureTask;

import java.util.ArrayList;
import java.util.concurrent.*;

/**
 * description :
 *
 * @author : Mr.Liuqh
 * @date : 2019-12-30 11:55
 */
public class FutureTaskTest {
    /**
     * 并行计算
     */
    public void testTask() {
        long beginTime = System.currentTimeMillis();
        // 实例化本类，用于后续使用内部类实例化
        FutureTaskTest futureTaskTest = new FutureTaskTest();

        // 创建任务列表
        ArrayList<FutureTask<Integer>> futureTaskList = new ArrayList<>();
        // 创建线程池
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        for (int i = 1; i <= 10; i++) {
            // 传入Callable对象创建FutureTask对象
            FutureTask<Integer> futureTask = new FutureTask<>(futureTaskTest.new ComputerTask(i));
            // 添加到任务列表
            futureTaskList.add(futureTask);
            // 提交给线程池执行任务，也可以通过exec.invokeAll(taskList)一次性提交所有任务
            executorService.submit(futureTask);
        }

        // 任务已经提交完
        System.out.println("任务已经提交完,主线程继续执行....");
        // 获取任务结果
        ArrayList<Integer> taskResultList = new ArrayList<>();
        for (FutureTask<Integer> futureTaskItem : futureTaskList) {
            try {
                // 获取任务执行结果，会造成主线程阻塞
                Integer total = futureTaskItem.get();
                taskResultList.add(total);
            } catch (ExecutionException | InterruptedException e) {
                e.printStackTrace();
            }
        }
        // 求和
        Integer integer = taskResultList.stream().reduce(Integer::sum).get();
        long useTime = (System.currentTimeMillis() - beginTime) / 1000L;
        System.out.println("10! + 9! + ...+1! =  " + integer );
        System.out.println(" 一共耗时:" + useTime + "秒");

        // 关闭线程池
        executorService.shutdown();
        System.out.println("任务执行完毕。");

    }

    /**
     * desc : 定义内部类
     **/
    private class ComputerTask implements Callable<Integer> {
        private Integer num;
        private Integer total;

        ComputerTask(Integer i) {
            this.num = i;
        }

        @Override
        public Integer call() throws Exception {
            total = 1;
            String s ="";
            for (int i = 1; i <= this.num; i++) {
                total *= i;
                s = s.concat(String.valueOf(i)).concat(" * ");
            }
            // 字符串截取
            String substring = s.substring(0,s.length()-2);
            // 休眠1秒钟，逾期主线程会继续执行，在主线程调用futureTask.get()，会阻塞，等待结果返回
            Thread.sleep(1000);
            System.out.println(substring + " = " + total);
            return total;
        }
    }
}

...
// 运行
new FutureTaskTest().testTask();

/*
输出:

任务已经提交完,主线程继续执行....
1 * 2  = 2
1 * 2 * 3 * 4 * 5 * 6 * 7  = 5040
1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9 * 10  = 3628800
1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9  = 362880
1 * 2 * 3 * 4 * 5 * 6 * 7 * 8  = 40320
1 * 2 * 3  = 6
1 * 2 * 3 * 4 * 5 * 6  = 720
1 * 2 * 3 * 4  = 24
1  = 1
1 * 2 * 3 * 4 * 5  = 120
10! + 9! + ...+1! =  4037913
 一共耗时:1秒
任务执行完毕。
```

## 3.2 串行实现
```
package com.hui.javalearn.thread.futureTask;

import java.util.ArrayList;
import java.util.concurrent.*;

/**
 * description :
 *
 * @author : Mr.Liuqh
 * @date : 2019-12-30 11:55
 */
public class FutureTaskTest {
    /**
     * desc : 定义内部类
     **/
    private class ComputerTask implements Callable<Integer> {
        private Integer num;
        private Integer total;

        ComputerTask(Integer i) {
            this.num = i;
        }

        @Override
        public Integer call() throws Exception {
            total = 1;
            String s ="";
            for (int i = 1; i <= this.num; i++) {
                total *= i;
                s = s.concat(String.valueOf(i)).concat(" * ");
            }
            // 字符串截取
            String substring = s.substring(0,s.length()-2);
            // 休眠1秒钟，逾期主线程会继续执行，在主线程调用futureTask.get()，会阻塞，等待结果返回
            Thread.sleep(1000);
            System.out.println(substring + " = " + total);
            return total;
        }
    }
    /**
    *  串行计算
    */
    public void testSerialTask() throws Exception {
        long beginTime = System.currentTimeMillis();
        FutureTaskTest futureTaskTest = new FutureTaskTest();
        ArrayList<Integer> taskResultList = new ArrayList<>();
        for (int i = 1; i <= 10; i++) {
            Integer call = futureTaskTest.new ComputerTask(i).call();
            taskResultList.add(call);
        }
        // 求和
        Integer integer = taskResultList.stream().reduce(Integer::sum).get();
        long useTime = (System.currentTimeMillis() - beginTime) / 1000L;
        System.out.println("10! + 9! + ...+1! =  " + integer );
        System.out.println("一共耗时:" + useTime + "秒");

    }
}

...

// 运行


/*
输出:

1  = 1
1 * 2  = 2
1 * 2 * 3  = 6
1 * 2 * 3 * 4  = 24
1 * 2 * 3 * 4 * 5  = 120
1 * 2 * 3 * 4 * 5 * 6  = 720
1 * 2 * 3 * 4 * 5 * 6 * 7  = 5040
1 * 2 * 3 * 4 * 5 * 6 * 7 * 8  = 40320
1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9  = 362880
1 * 2 * 3 * 4 * 5 * 6 * 7 * 8 * 9 * 10  = 3628800
10! + 9! + ...+1! =  4037913
一共耗时:10秒

```

[查看源码](https://github.com/52lu/java-learn/blob/master/src/main/java/com/hui/javalearn/thread/futureTask/FutureTaskTest.java)

# 4.参考链接

- [FutureTask的用法及两种常用的使用场景](https://cloud.tencent.com/developer/article/1041329)
- [可取消的异步任务——FutureTask用法及解析](https://www.jianshu.com/p/55221d045f39)


