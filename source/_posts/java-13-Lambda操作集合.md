---
title: Java-基础篇-Lambda操作集合
date: 2019-12-13 21:20:10
tags: 
 - Java
categories:
 - 后端编程
---

# 1. Lambda操作Collections
为引入Lambda表达式，Java8新增了java.util.funcion包，里面包含常用的函数接口，这是Lambda表达式的基础，Java集合框架也新增部分接口，以便与Lambda表达式对接。由于继承关系，他们相应的子类也都会继承这些新方法

**框架的接口继承结构**
![](https://52lu.github.io/directionsImg/java/collection-extends-tree.jpeg)

## 1.1 Java8新加入的方法


接口类型 | 新加入的方法
---|---
Collection | removeIf() forEach() spliterator() stream() parallelStream() 
List | replaceAll() sort()
Map	| getOrDefault() forEach() replaceAll() putIfAbsent() remove() replace() computeIfAbsent() computeIfPresent() compute() merge()

> 由于继承关系，他们相应的子类也都会继承这些新方法。

# 2. 在Collection中使用Lambda
## 2.1 removeIf()
该方法签名为boolean removeIf(Predicate<? super E> filter),作用是删除容器中所有满足filter指定条件的元素。


- 使用示例:
删除集合中小于100的元素

**java8以前的代码**
```
ArrayList<Integer> integers = new ArrayList<>(Arrays.asList(20, 420, 170, 100, 140));
Iterator<Integer> iterator = integers.iterator();
while (iterator.hasNext()){
    if (iterator.next() < 100){
        iterator.remove();
    }
}
System.out.println(integers);

/**
* 输出:[420, 170, 100, 140]
*/
```

**匿名内部类过度代码**

```
ArrayList<Integer> integers = new ArrayList<>(Arrays.asList(20, 420, 170, 100, 140));
integers.removeIf(new Predicate<Integer>(){ 
    @Override
    public boolean test(Integer number){
        return number < 100;
    }
});
```

**Lambda代码**
```
ArrayList<Integer> integers = new ArrayList<>(Arrays.asList(20, 420, 170, 100, 140));
integers.removeIf(number -> number < 100);
```

**改写依据**
removeIf要传入一个Predicate函数接口的实现类，Predicate是一个函数接口，里面只有一个待实现方法boolean test(T t)。根据Lambda的改写依据，可以省略函数接口实现类的类名和方法名，有参时，直接使用: 参数->{...},由于是单行代码块，{} 也可以省略

## 2.2 forEach()
签名为void forEach(Consumer<? super E> action)，作用是对容器中的每个元素执行action指定的动作，其中Consumer是个函数接口。

- 使用示例:
假设有一个字符串列表，需要打印出其中所有长度小于3的字符串.

**java8以前的代码**
```java
// 使用曾强for循环迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("PHP","JAVA","PYTHON","GO"));
for(String str : list){
    if(str.length() < 3){
      System.out.println(str);   
    }
}

//输出:GO
```
**匿名内部类过度代码**
```java
// 使用forEach()结合匿名内部类迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("PHP","JAVA","PYTHON","GO"));
list.forEach(new Consumer<String>(){
    @Override
    public void accept(String str){
        if(str.length() < 3)
            System.out.println(str);
    }
});
```

**Lambda代码**
```java
ArrayList<String> arrayList = new ArrayList<>(Arrays.asList("PHP", "JAVA", "PYTHON", "GO"));
arrayList.forEach(str ->{
    if (str.length() < 3){
        System.out.println(str);
    }
});

//输出:GO
```

## 2.3 replaceAll()
该方法签名为void replaceAll(UnaryOperator<E> operator)，作用是对每个元素执行operator指定的操作，并用操作结果来替换原来的元素。其中UnaryOperator是一个函数接口，里面只有一个待实现函数T apply(T t)。


- 使用示例:
假设有一个字符串列表，将其中所有长度小于3的字符串 转为大写.

**java8以前的代码**
```java
ArrayList<String> list = new ArrayList<>(Arrays.asList("PHP", "JAVA", "PYTHON", "GO"));
for (int i = 0; i < list.size(); i++) {
    String str = list.get(i);
    if (str.length() < 3) {
        list.set(i, str.toLowerCase());
    }
}
System.out.println(list);
//输出: [PHP, JAVA, PYTHON, go]
```
**匿名内部类过度代码**
```java
// 使用forEach()结合匿名内部类迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("PHP","JAVA","PYTHON","GO"));
list.replaceAll(new UnaryOperator<String>(){
    @Override
    public String apply(String str){
        if(str.length() < 3){
            return str.toLowerCase(); 
        }
        return str;
    }
});
System.out.println(list);
//输出: [PHP, JAVA, PYTHON, go]
```

**Lambda代码**
```java
ArrayList<String> arrayList = new ArrayList<>(Arrays.asList("PHP", "JAVA", "PYTHON", "GO"));
list.replaceAll(str -> {
    if (str.length() < 3 ) {
        return str.toLowerCase();
    }
    return str;
});
System.out.println(list);
//输出: [PHP, JAVA, PYTHON, go]
```



## 2.4 sort()
该方法定义在List接口中，方法签名为void sort(Comparator<? super E> c)，该方法根据c指定的比较规则对容器元素进行排序。Comparator接口我们并不陌生，其中有一个方法int compare(T o1, T o2)需要实现，显然该接口是个函数接口。


- 使用示例:
假设有一个字符串列表，按照字符串长度对元素降序排序。

**java8以前的代码**
```java
ArrayList<String> list = new ArrayList<>(Arrays.asList("PHP", "JAVA", "PYTHON", "GO"));
Collections.sort(list, new Comparator<String>(){
    @Override
    public int compare(String str1, String str2){
        return str2.length()-str1.length();
    }
});
System.out.println(list);
//输出: [PYTHON, JAVA, PHP, GO]
```

**Lambda代码**
```java
list.sort((str1,str2)->str2.length() - str1.length());
//输出: [PHP, JAVA, PYTHON, go]
```

## 2.5 spliterator()
方法签名为Spliterator<E> spliterator()，该方法返回容器的可拆分迭代器。从名字来看该方法跟iterator()方法有点像，我们知道Iterator是用来迭代容器的，Spliterator也有类似作用，但二者有如下不同：
- Spliterator既可以像Iterator那样逐个迭代，也可以批量迭代。批量迭代可以降低迭代的开销。
- Spliterator是可拆分的，一个Spliterator可以通过调用Spliterator<T> trySplit()方法来尝试分成两个。一个是this，另一个是新返回的那个，这两个迭代器代表的元素没有重叠。

可通过（多次）调用Spliterator.trySplit()方法来分解负载，以便多线程处理。

## 2.6 stream()和parallelStream()
stream()和parallelStream()分别返回该容器的Stream视图表示，不同之处在于parallelStream()返回并行的Stream。Stream是Java函数式编程的核心类，我们会在后面章节中学习。

> 以上内容摘自[关于Java Lambda表达式看这一篇就够了](http://objcoding.com/2019/03/04/lambda/#stream%E5%92%8Cparallelstream)