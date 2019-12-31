---
title: Java-基础篇-初始化块
date: 2019-10-15 20:19:48
tags:
 - Java
categories:
 - 后端编程
---
# 1.什么是初始化块
Java使用构造器来对单个对象进行初始化操作，使用构造器先完成整个Java对象的状态初始化，然后将Java对象返回给程序，从而让该Java对象的信息更加完整。<font color=green>与构造器作用非常类似的是初始化块，它也可以对Java对象进行初始化操作


# 2. 使用初始化块
`初始化块是Java类里可出现的第4种成员（前面依次有Field、方法和构造器）。`

## 2.1 语法
```java
 [修饰符] {
     // todo 可执行性代码
 }
```

**@注意:**
- 一个类里可以有多个初始化块，相同类型的初始化块之间有顺序:前面定义的初始化块先执行，后面定义的初始化块后执行。
- 初始化块的修饰符只能是static，使用static修饰的初始化块被称为静态初始化块。
- 初始化块里的代码可以包含任何可执行性语句，包括定义局部变量、调用其他对象的方法，以及使用分支、循环语句等。
- 初始化块虽然也是Java类的一种成员，但它没有名字，也就没有标识，因此无法通过类、对象来调用初始化块。
- 初始化块只在创建Java对象时隐式执行，而且在执行构造器之前执行

## 2.2 示例

定义一个类,然后实例化该类

```
package com.hui.javalearn.classes;

/**
 * description : 初始化块使用
 *
 * @author : Mr.Liuqh
 * @date : 2019-12-31 11:57
 */
public class StaticUseOne {
    /**
    *  定义一个静态成员
    */
    private  int num;

    /*
    * 定义第一个初始化块
    */
    {
        // 对成员变量赋值
        num = 11;
        System.out.println("第一个初始化块，输出: " + num);
    }

    /*
     * 定义第二个初始化块
     */
    {
        System.out.println("第二个初始化块，输出: " + num);
    }

    /**
    *  无参构造方法
    */
    public StaticUseOne(){
        System.out.println("无参构造方法输出: " + num);
    }

}

...

// 测试
new StaticUseOne();

/*
输出:
第一个初始化块，输出: 11
第二个初始化块，输出: 11
无参构造方法输出: 11
```
[查看源码](https://github.com/52lu/java-learn/blob/master/src/main/java/com/hui/javalearn/classes/StaticUseOne.java)


> <font color=orange>虽然Java允许一个类里定义2个普通初始化块，但这没有任何意义。因为初始化块是在创建Java对象时隐式执行的，而且它们总是全部执行，因此我们完全可以把多个普通初始化块合并成一个初始化块，从而可以让程序更加简洁，可读性更强



# 3.静态初始化块
如果定义初始化块时使用了static修饰符，则这个初始化块就变成了静态初始化块，也被称为类初始化块。

<font color=red>静态初始化块是类相关的，系统将在类初始化阶段执行静态初始化块，而不是在创建对象时才执行。因此静态初始化块总是比普通初始化块先执行。


## 3.1 示例

```
package com.hui.javalearn.classes;

/**
 * description : 静态初始化块使用
 *
 * @author : Mr.Liuqh
 * @date : 2019-12-31 11:57
 */
public class StaticUseTwo {
    /**
    *  定义一个静态成员
    */
    private static int num;

    /**
     *  定义一个非静态成员
     */
    private int total;

    /*
     * 定义一个初始化块，
     *
     * 注意: 虽然它是第一个被定义的初始化块，但不会第一个执行。
     */
    {
        // 给非静态成员赋值
        total = 100;
        System.out.println("这是一个非静态初始化块，输出: num = " + num);
        System.out.println("这是一个非静态初始化块，输出: total = " + total);
    }

    /*
    * 定义第一个静态初始化块
    *
    * 注意: 会第一个执行！！
    */
    static {
        // 对静态成员变量赋值
        num = 11;
        System.out.println("这是一个静态初始化块，输出: " + num);
    }

    /**
    *  无参构造方法
    */
    public StaticUseTwo(){
        System.out.println("无参构造方法输出: " + num);
    }

}

// 测试
new StaticUseTwo();

/* 
输出:
这是一个静态初始化块，输出: 11
这是一个非静态初始化块，输出: num = 11
这是一个非静态初始化块，输出: total = 100
无参构造方法输出: 11
```
[查看源码](https://github.com/52lu/java-learn/blob/master/src/main/java/com/hui/javalearn/classes/StaticUseTwo.java)

## 3.2 总结

- 静态初始化块总是比普通初始化块先执行。
- 静态初始化不能访问非静态成员，也不能访问实例Field和实例方法。



# 4. 使用场景
从某种程度上来看，初始化块是构造器的补充，初始化块总是在构造器执行之前执行。

<font color=red>与构造器不同的是，初始化块是一段固定执行的代码，它不能接收任何参数。</font>因此初始化块对同一个类的所有对象所进行的初始化处理完全相同。基于这个原因，不难发现初始化块的基本用法，<font color=green>如果有一段初始化处理代码对所有对象完全相同，且无须接收任何参数，就可以把这段初始化处理代码提取到初始化块中.


## 4.1 示例
如果两个构造器中有相同的初始化代码，这些初始化代码无须接收参数，就可以把它们放在初始化块中定义，如下图所示:


![](https://52lu.github.io/images/java/static-use-scenes.png)
