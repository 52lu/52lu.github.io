---
title: Java-基础篇-异常处理
date: 2019-11-11 18:14:17
tags:
 - Java
categories:
 - 后端编程
---

# 1. 异常概述

Java的异常处理机制也秉承着面向对象的基本思想。在Java中，所有的异常都是以类的类型存在。除了内置的异常类之外，Java也可以自定义异常类。此外，Java的异常处理机制也允许自定义抛出异常。

# 2. 处理错误特点
- 不需要打乱程序的结构，如果没有任何错误产生，那么程序的运行不受任何影响。
- 不依靠方法的返回值来报告错误是否产生。

- 采用集中的方式处理错误，能够根据错误种类的不同来进行对应的错误处理操作。


# 3. 常见的异常

![](https://52lu.github.io/directionsImg/java/common-error-1.png)
![](https://52lu.github.io/directionsImg/java/common-error-2.png)

# 4. 抛出异常
## 4.1 thorws关键字
在方法中使用thorws关键字抛出错误

**语法**
```
[权限修饰符] 返回类型 方法名 throws 具体错误类{
    ....
}
```

**示例**
```
public class Main {

    public static void main(String[] args) {
        System.out.println(divsion(10,0));
    }
    /**
     * description: 测试除法异常
     **/
    public static int  divsion(int a, int b ) throws ArithmeticException{
        return  a / b ;
    }
}

/** 
* 输出:

Exception in thread "main" java.lang.ArithmeticException: / by zero
	at Main.divsion(Main.java:27)
	at Main.main(Main.java:17)
**/

```

## 4.2 thorw关键字

**使用示例**
```
public class Main {

    public static void main(String[] args) {
        System.out.println(divsion(10,0));
    }
    /**
     * description: 测试除法异常
     **/
    public static int  divsion(int a, int b ) throws ArithmeticException{
        if (b == 0){
            throw new ArithmeticException("除数不能为0！");
        }
        return  a / b ;
    }
}

/** 
* 输出:

Exception in thread "main" java.lang.ArithmeticException: 除数不能为0！
	at Main.divsion(Main.java:47)
	at Main.main(Main.java:39)
**/

```

# 5. 捕捉异常
异常捕获处理是由try、catch与finally等3个关键字所组成的程序块，其语法如下所示
## 5.1 try-catch
```
try {
    // todo ...
} catch (ArithmeticException e) {
    // 捕获到对应的异常
}
```
## 5.2 try-catch-catch
```
try {
    // todo ...
} catch (ArithmeticException e) {
    // 捕获到对应的异常
} catch (ArithmeticException e) {
    // 捕获到对应的异常
}
```

## 5.3 try-finally
```
try {
    // todo ...
} finally (ArithmeticException e) {
   // 总会处理的逻辑
}
```

## 5.4  try-catch-finally
```
try {
    // todo ...
} catch (ArithmeticException e) {
    // 捕获到对应的异常
} finally (ArithmeticException e) {
  // 总会处理的逻辑
}
```

# 6. 异常类关系图
![](https://52lu.github.io/directionsImg/java/throwable-tree.png)

## 6.1 异常类型的继承关系
异常类型的最大父类是Throwable类，其分为两个子类，分别为Exception、Error。Exception表示程序可处理的异常，而Error表示JVM错误，一般无需程序开发人员自己处理。

## 6.2 RuntimeException和Exception的区别
- RuntimeException类是Exception类的子类。
- Exception定义的类，强制性要求用户必须处理 
- RuntimeException定义的异常可以选择性地进行处理。


# 7.自定义异常类

**自定义类的语法:**
```
class 异常类名 extends Exception {
    ....
}
```

**使用示例:**
```
public class userDefinedException
{
  public static void main(String[]args)
  {
    try {
       throw new MyException("自定义异常--仅为测试演示！");
     } catch(MyException e){
      System.out.println(e);
    }
  }
}


class MyException extends Exception
{
  public MyException(String msg)
  {
    super(msg); //调用Exception类的构造方法，存入异常信息
  }
}
```