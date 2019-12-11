---
title: Java-基础篇-初识Lambda
date: 2019-12-11 21:20:10
tags: 
 - Java
categories:
 - 后端编程
---


# 1. 介绍
Lambda表达式是Java8的新特性，一个最为重要用法是简化某些匿名内部类的写法。在加上结合新增的的流相关的 API使用，让我这个java小白觉得相当牛x，至于多牛x，上代码体验。一起学习吧..

# 2. Lambda表达式使用
## 2.1 无参函数的简写

**需求:** 新建一个线程

- Java8以前的代码

```java
new Thread(new Runnable(){
	// 接口名
	@Override
	public void run(){
	  // 方法名
	  System.out.println("Thread1 run()");
	}
}
).start();
```
- 采用Lambda简写

```java
// 单行代码
new Thread(
  () -> System.out.println("Thread1 run()")
).start();

// 多行代码块时的写法
new Thread(
  () -> {
      System.out.println("Hello Thread1 ")；
      System.out.println("Thread1 run()")
  }
).start();

```
**对比:**
1. 省略接口名函数名
2. 不用再定义接口Runnable的匿名对象

## 2.2 有参函数的简写
**需求:** 给一个字符串列表通过自定义比较器，按照字符串长度进行排序

- Java8以前的代码

```java
List<String> list = Arrays.asList("PHP", "JAVA", "GO", "PYTHON");
// 定义一个实现Comparator接口的，匿名类
Collections.sort(list, new Comparator<String>(){
    // 重载接口中的方法
    @Override
    public int compare(String s1, String s2){
        if(s1 == null)
            return -1;
        if(s2 == null)
            return 1;
        return s1.length()-s2.length();
    }
});
```
- 采用Lambda简写

```java
// JDK8 Lambda表达式写法
List<String> list = Arrays.asList("PHP", "JAVA", "GO", "PYTHON");
// 省略Comparator接口实现类、compare方法、参数的类型
Collections.sort(list, (s1, s2) ->{
    if(s1 == null)
        return -1;
    if(s2 == null)
        return 1;
    return s1.length()-s2.length();
});
```


## 2.3 更多合法的书写形式
```
// Lambda表达式的书写形式
Runnable run = () -> System.out.println("Hello World");// 1
ActionListener listener = event -> System.out.println("button clicked");// 2
Runnable multiLine = () -> {// 3 代码块
    System.out.print("Hello");
    System.out.println(" Hoolee");
};
BinaryOperator<Long> add = (Long x, Long y) -> x + y;// 4
BinaryOperator<Long> addImplicit = (x, y) -> x + y;// 5 类型推断
```
- 1处展示了无参函数的简写
- 2处展示了有参函数的简写，以及类型推断机制
- 3处是代码块的写法
- 4处和5处再次展示了类型推断机制


# 3.简写依据
## 3.1 依据一:函数接口

<font color=red> 能够使用Lambda的依据是必须有相应的函数接口（函数接口，是指内部只有一个抽象方法的接口）</font>。这意味着你并不能在代码的任何地方任性的写Lambda表达式

## 3.2 依据二:类型推断机制
Lambda表达式另一个依据是类型推断机制，在上下文信息足够的情况下，编译器可以推断出参数表的类型，而不需要显式指名
