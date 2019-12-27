---
title: Java-基础篇-Collect方法详解
date: 2019-12-16 13:17:20
tags: 
 - Java
categories:
 - 后端编程
---

# 1 collect是什么
collect是对流(Stream)进行mutable reduction(可变缩减操作)操作的接口函数，已经有很多文章介绍collect多么牛逼，多么强大。对于初学Java的我这里只总结怎么用；
想了解更多的，可以查看以下链接。

- [终极武器collect()](http://objcoding.com/2019/03/04/lambda/#%E7%BB%88%E6%9E%81%E6%AD%A6%E5%99%A8collect)

** 签名 **

有如下两个签名
```java
// 第一种
<R> R collect(Supplier<R> supplier,
                  BiConsumer<R, ? super T> accumulator,
                  BiConsumer<R, R> combiner);

// 第二种
<R, A> R collect(Collector<? super T, A, R> collector);
```

# 2. Collector收集器

<font color=blue>收集器（Collector）是为Stream.collect()方法量身打造的工具接口（类）。</font>考虑一下将一个Stream转换成一个容器（或者Map）需要做哪些工作？我们至少需要两样东西：

- 目标容器是什么？是ArrayList还是HashSet，或者是个TreeMap。
- 新元素如何添加到容器中？是List.add()还是Map.put()。
- 如果并行的进行规约，还需要告诉collect(),多个部分结果如何合并成一个。

结合以上分析，collect()方法定义为
```
<R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)，
```
<font color=red>三个参数依次对应上述三条分析。不过每次调用collect()都要传入这三个参数太麻烦，收集器Collector就是对这三个参数的简单封装,</font>所以collect()的另一定义为`<R,A> R collect(Collector<? super T,A,R> collector)`。Collectors工具类可通过静态方法生成各种常用的Collector。

# 3. 具体使用

## 3.1 toList/toSet : 转成集合
```
List<Integer> integerList = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
// toList操作
List<Integer> collectList = integerList.stream().filter(integer -> integer % 3 == 0).collect(Collectors.toList());
System.out.println("toList结果 -> " + collectList + "");
//  输出: toList结果 -> [3, 6, 9]

// toSet操作
Set<Integer> collectSet = integerList.stream().filter(integer -> integer % 2 == 0).collect(Collectors.toSet());
System.out.println("toSet结果 -> " + collectSet + "");
// 输出: toSet结果 -> [2, 4, 6, 8]
```
## 3.2 toCollection : 转换成特定的集合
```java
List<Integer> integers = Arrays.asList(11, 22, 14, 6, 9);
ArrayList<Integer> arrayList = integers.stream().collect(Collectors.toCollection(ArrayList::new));
System.out.println("toCollection 转ArrayList -> " + arrayList + "");
//  输出: toCollection 转ArrayList -> [11, 22, 14, 6, 9]

HashSet<Integer> hashSet = integers.stream().collect(Collectors.toCollection(HashSet::new));
System.out.println("toCollection 转HashSet -> " + hashSet + "");
//  输出: toCollection 转HashSet -> [22, 6, 9, 11, 14]

TreeSet<Integer> treeSet = integers.stream().collect(Collectors.toCollection(TreeSet::new));
System.out.println("toCollection 转TreeSet -> " + treeSet + "");
//  输出: toCollection 转TreeSet -> [6, 9, 11, 14, 22]

```

## 3.3 toMap : 转成map

### 1.结合实体类
```java
public class Main {

    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
                new Employee("张三", 18),
                new Employee("李四", 23),
                new Employee("王麻子", 30)
        );

        Map<String, Integer> collect = employees.stream().collect(Collectors.toMap(Employee::getName, Employee::getAge));
        System.out.println("toMap -> " + collect + "");
        // 输出: toMap -> {李四=23, 张三=18, 王麻子=30}

    }
    
    static class Employee {
        private String name;
        private int age;

        public Employee(String name, int age) {
            setName(name);
            setAge(age);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        @Override
        public String toString() {
            StringBuilder str = null;
            str = new StringBuilder();
            str.append(" Name:- " + getName() + " Age:- " + getAge());
            return str.toString();
        }
    }
}
```

### 2.集合直接转
```java
List<Integer> integers = Arrays.asList(1, 2, 4, 6, 9);
Map<Integer, String> collect = integers.stream().collect(Collectors.toMap(x -> x, x -> String.valueOf(x)));
System.out.println("集合转toMap -> " + collect + "");
//  输出: 集合转toMap -> {1=1, 2=2, 4=4, 6=6, 9=9}
```

## 3.4 averagingInt: 求平均值
```
List<Integer> integers = Arrays.asList(11, 22, 14, 6, 9);
Double collect = integers.stream().collect(Collectors.averagingInt(x -> x));
System.out.println("平均值 -> " + collect + "");
//  输出: 平均值 -> 12.4
```
## 3.5 joining : 拼接

**三种签名:**
```java
//第一种:直接拼接
public static Collector<CharSequence,?,String> joining()

// 第二种:以delimiter作为分隔符拼接
public static Collector<CharSequence,?,String> joining(CharSequence delimiter)

// 第三种:带有分隔符(delimiter)、前缀(prefix)、后缀(suffix)、拼接
public static Collector<CharSequence,?,String> joining(CharSequence delimiter,
                                                       CharSequence prefix,
                                                       CharSequence suffix)
```

**代码**
```java
// 声明一个字符串list
List<String> strings = Arrays.asList("php", "is", "the", "best","language", "!");
// 第一种签名 直接拼接
String joining1 = strings.stream().collect(Collectors.joining());
System.out.println("第一种签名， 直接拼接 -->  " + joining1 + " ");
// 第二种签名拼接(以 | 作为分隔符)
String joining2 = strings.stream().collect(Collectors.joining("|"));
System.out.println("第二种签名，带有分隔符拼接 -->  " + joining2 + " ");
// 第三种签名，带有分隔符(|)、前缀(preFix_)、后缀(_suffix)、拼接
String joining3 = strings.stream().collect(Collectors.joining("|", "preFix_", "_suffix"));
System.out.println("第三种签名，带有分隔符、前缀(preFix_)、后缀(_suffix)、拼接 -->  " + joining3 + " ");

/**
输出:

第一种签名， 直接拼接 -->  phpisthebestlanguage! 
第二种签名，带有分隔符拼接 -->  php|is|the|best|language|! 
第三种签名，带有分隔符、前缀(preFix_)、后缀(_suffix)、拼接 -->  preFix_php|is|the|best|language|!_suffix
*/
```

## 3.6 groupingBy : 分组

**三种签名:**
```java
// 第一种: 
public static <T,K> Collector<T,?,Map<K,List<T>>> groupingBy(Function<? super T,? extends K> classifier)

// 第二种:提供后续收集器(Collector)参数
public static <T,K,A,D> Collector<T,?,Map<K,D>> groupingBy(Function<? super T,? extends K> classifier,
                                                           Collector<? super T,A,D> downstream)
// 第三种:指定特定Map类型作为返回值 
public static <T,K,D,A,M extends Map<K,D>> Collector<T,?,M> groupingBy(Function<? super T,? extends K> classifier,
                                                                       Supplier<M> mapFactory,
                                                                       Collector<? super T,A,D> downstream)
```

**代码**

employees 类
```
class Employee {
   private String name;
   private int age;
   private String sex;
   public Employee(String name, int age,String sex) {
       setName(name);
       setAge(age);
       setSex(sex);
   }
   public String getName() {
       return name;
   }
   public void setName(String name) {
       this.name = name;
   }
   public int getAge() {
       return age;
   }
   public void setAge(int age) {
       this.age = age;
   }
   public String getSex() {
       return sex;
   }
   public void setSex(String age) {
       this.sex = age;
   }
   @Override
   public String toString() {
       StringBuilder str = null;
       str = new StringBuilder();
       str.append(" Name:- " + getName() + " Age:- " + getAge());
       return str.toString();
   }
}
```



- 第一种签名

```
// 声明一个成员list
List<Employee> employees = Arrays.asList(
        new Employee("张颖", 16,"女"),
        new Employee("小英", 25,"女"),
        new Employee("王慧", 22,"女"),
        new Employee("李四", 23,"男"),
        new Employee("王麻子", 27,"男")
);
// 根据性别分组
Map<String, List<Employee>> collect = employees.stream().collect(Collectors.groupingBy(Employee::getSex));
System.out.println("根据性别分组 : \n " + collect + "\n");

// 根据年龄分组
Map<Integer, List<Employee>> collect11 = employees.stream().collect(Collectors.groupingBy(Employee::getAge));
System.out.println("根据年龄分组 : \n " + collect11 + "\n");

/**
输出:
根据性别分组 : 
 {女=[ Name:- 张颖 Age:- 16,  Name:- 小英 Age:- 25,  Name:- 王慧 Age:- 22], 男=[ Name:- 李四 Age:- 23,  Name:- 王麻子 Age:- 27]}

根据年龄分组 : 
 {16=[ Name:- 张颖 Age:- 16], 22=[ Name:- 王慧 Age:- 22], 23=[ Name:- 李四 Age:- 23], 25=[ Name:- 小英 Age:- 25], 27=[ Name:- 王麻子 Age:- 27]}

```

- 第二种签名

```
// 声明一个成员list
List<Employee> employees = Arrays.asList(
        new Employee("张颖", 16,"女"),
        new Employee("小英", 25,"女"),
        new Employee("王慧", 22,"女"),
        new Employee("李四", 23,"男"),
        new Employee("王麻子", 27,"男")
);
// 根据性别分组后，统计每组的count
Map<String, Long> collect1 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, Collectors.counting()));
System.out.println("根据性别分组后，统计每组的count : \n " + collect1 + "\n");
//  根据性别分组后，计算每组的平均年龄
Map<String, Double> collect2 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, Collectors.averagingInt(Employee::getAge)));
System.out.println("根据性别分组后，计算每组的平均年龄: \n " + collect2 + "\n");
// 根据性别分组后，找出每组最大年龄
Map<String, Optional<Employee>> collect5 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, Collectors.maxBy(Comparator.comparingInt(Employee::getAge))));
System.out.println("根据性别分组后，找出每组最大年龄 : \n " + collect5 + "\n");
// 根据性别、年龄分组(多个字段分组)
Map<String, Map<Integer, List<Employee>>> collect3 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, Collectors.groupingBy(Employee::getAge)));
System.out.println("根据性别、年龄分组: \n " + collect3 + "\n");
//  根据性别、年龄分组(多个字段分组)后，统计每个年龄组的人数
Map<String, Map<Integer, Long>> collect4 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, Collectors.groupingBy(Employee::getAge, Collectors.counting())));
System.out.println("根据性别、年龄分组后，统计每组数量 : \n " + collect4 + "\n");

/**
输出:
根据性别分组后，计算每组的平均年龄: 
 {女=21.0, 男=25.0}

根据性别分组后，找出每组最大年龄 : 
 {女=Optional[ Name:- 小英 Age:- 25], 男=Optional[ Name:- 王麻子 Age:- 27]}

根据性别、年龄分组: 
 {女={16=[ Name:- 张颖 Age:- 16], 22=[ Name:- 王慧 Age:- 22], 25=[ Name:- 小英 Age:- 25]}, 男={23=[ Name:- 李四 Age:- 23], 27=[ Name:- 王麻子 Age:- 27]}}

根据性别、年龄分组后，统计每组数量 : 
 {女={16=1, 22=1, 25=1}, 男={23=1, 27=1}}
```


- 第三种签名

```
// 声明一个成员list
List<Employee> employees = Arrays.asList(
        new Employee("张颖", 16,"女"),
        new Employee("小英", 25,"女"),
        new Employee("王慧", 22,"女"),
        new Employee("李四", 23,"男"),
        new Employee("王麻子", 27,"男")
);

// 根据性别分组并指定返回类型为HashMap
HashMap<String, List<Employee>> collect6 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, HashMap::new, Collectors.toList()));
System.out.println("根据性别分组并指定返回类型为HashMap : \n " + collect6 + "\n");

// 根据性别分组并、指定返回类型为HashMap,且分组中只显示成员姓名
HashMap<String, List<String>> collect7 = employees.stream().collect(Collectors.groupingBy(Employee::getSex, HashMap::new, Collectors.mapping(Employee::getName, Collectors.toList())));
System.out.println("根据性别分组并、指定返回类型为HashMap,且分组中只显示成员姓名 : \n" + collect7 + "\n");

/**
输出:

根据性别分组并指定返回类型为HashMap : 
 {女=[ Name:- 张颖 Age:- 16,  Name:- 小英 Age:- 25,  Name:- 王慧 Age:- 22], 男=[ Name:- 李四 Age:- 23,  Name:- 王麻子 Age:- 27]}

根据性别分组并、指定返回类型为HashMap,且分组中只显示成员姓名 : 
{女=[张颖, 小英, 王慧], 男=[李四, 王麻子]}
```


## 3.7 partitioningBy : 分组

作用: 把数据分成两部分,key为ture/false。共有两种签名

**签名**
```java
// 第一种
public static <T> Collector<T,?,Map<Boolean,List<T>>> partitioningBy(Predicate<? super T> predicate)

// 第二种
public static <T,D,A> Collector<T,?,Map<Boolean,D>> partitioningBy(Predicate<? super T> predicate,
                                                                   Collector<? super T,A,D> downstream)
```

**代码**
```java
// 声明一个List
List<Integer> integers = Arrays.asList(1, 21, 81, 23, 42, 90, 62, 37);

// 第一种签名
// 把数据根据能否被2整除，进行分组
Map<Boolean, List<Integer>> collect = integers.stream().collect(Collectors.partitioningBy(x -> x % 2 ==0));
System.out.println("能否被2整除，进行分组 -> " + collect + "");


// 第二种签名
// 能否被2整除，进行分组，并统计每组数量 
Map<Boolean, Long> collect1 = integers.stream().collect(Collectors.partitioningBy(x -> x % 2 == 0, Collectors.counting()));
System.out.println("能否被2整除，进行分组，并统计每组数量 -> " + collect1 + "");

/**
输出:
  能否被2整除，进行分组 -> {false=[1, 21, 81, 23, 37], true=[42, 90, 62]}
  能否被2整除，进行分组，并统计每组数量 -> {false=5, true=3}
```



# 4.更多方法

![](https://52lu.github.io/images/java8-zh-collectors.png)

[查看文档，更多方法](https://www.tbaqkj.com/javase/8/docs/api/)