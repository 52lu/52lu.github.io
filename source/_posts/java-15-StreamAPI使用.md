---
title: Java-基础篇-Stream使用
date: 2019-12-15 21:47:18
tags: 
 - Java
categories:
 - 后端编程
---


# 1. 为什么需要 Stream
Stream 作为 Java 8 的一大亮点，它与 java.io 包里的 InputStream 和 OutputStream 是完全不同的概念。它也不同于 StAX 对 XML 解析的 Stream，也不是 Amazon Kinesis 对大数据实时处理的 Stream。Java 8 中的 Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。<font color =red>同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错, 但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。</font>

# 2. 常见的stream接口继承关系图

![](https://52lu.github.io/images/java-stream-interface.png)

图中4种stream接口继承自BaseStream，其中IntStream, LongStream, DoubleStream对应三种基本类型（int, long, double，注意不是包装类型），Stream对应所有剩余类型的stream视图。为不同数据类型设置不同stream接口，可以1.提高性能，2.增加特定接口函数。


> <font color=orange> 需要注意的是，IntStream, LongStream, DoubleStream 并不是Stream的子接口</font>

**疑问:为什么不把IntStream, LongStream, DoubleStream 设计成Stream的子接口呢?**

`原因:这些方法的名字虽然相同，但是返回类型不同，如果设计成父子接口关系，这些方法将不能共存，因为Java不允许只有返回类型不同的方法重载。`

# 3. 如何得到一个stream？

stream并不是某种数据结构，它只是数据源的一种视图。这里的数据源可以是一个数组，Java容器或I/O channel等。<font color=blue>正因为如此要得到一个stream通常不会手动创建，而是调用对应的工具方法。</font>

## 3.1 获取stream方法

- 调用Collection.stream()方法
- 调用Collection.parallelStream()方法
- 调用Arrays.stream(T[] array)方法
- 直接使用Stream.of()方法就能从一组对象创建一个stream对象

# 4. stream和collections区别
虽然大部分情况下stream是容器调用Collection.stream()方法得到的，但stream和collections有以下不同：

- **无存储。** stream不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java容器或I/O channel等。
- **为函数式编程而生。** 对stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新stream。
- **惰式执行。** stream上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
- **可消费性。** stream只能被“消费”一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

# 5.操作stream类型
对stream的操作分为为两类，**中间操作(intermediate operations)和结束操作(terminal operations)，** 二者特点是：

- 中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream，仅此而已。
- 结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。

> <font color=green>区分中间操作和结束操作最简单的方法，就是看方法的返回值，返回值为stream的大都是中间操作，否则是结束操作。</font>

# 6.操作stream接口方法

操作类型 | 接口方法
---|---
中间操作  | concat()、distinct()、filter()、flatMap()、limit()、map()、peek()、skip()、sorted()、parallel()、sequential()、unordered()
结束操作 | allMatch()、anyMatch()、collect()、count()、findAny()、findFirst()、forEach()、forEachOrdered()、max()、min()、noneMatch()、reduce()、toArray()

# 7.stream方法使用
stream跟函数接口关系非常紧密，没有函数接口stream就无法工作。<font color=purple>【函数接口是指内部只有一个抽象方法的接口。通常函数接口出现的地方都可以使用Lambda表达式，所以不必记忆函数接口的名字。】</font>
## 7.1 中间操作方法
### 1. concat() : 合并两个stream

**签名:**
```
static <T> Stream<T> concat(Stream<? extends T> a,tream<? extends T> b)
```
**作用:**
- 创建一个延迟连接的流，其元素是a的所有元素和b的所有元素。
- 如果两个输入流都是有序的，则对所得到的流进行排序。
- 如果任一输入流是并行的，则得到的流是平行的。
- 关闭结果流时，将调用两个输入流的关闭处理程序。

**代码:**
```
public static void main(String[] args) {
    // 创建两个stream对象
    Stream<String> language1 = Stream.of("PHP", "JAVA");
    Stream<String> language2 = Stream.of("GO", "PYTHON");
    // 使用中间操作concat，合并两个stream对象
    Stream<String> concat = Stream.concat(language1, language2);
    // 使用结束操作collect,转成list
    List<String> collect = concat.collect(Collectors.toList());
    System.out.println(collect);
    // 输出: [PHP, JAVA, GO, PYTHON]
}
```
[更多concat用例](http://www.leftso.com/blog/613.html)

### 2. distinct() : 去重
**说明:**
返回:返回由该流的不同元素（根据 Object.equals(Object) ）组成的Stream。

**代码:**
```
List<String> strings = Arrays.asList("AA", "BB", "CC", "AA", "BB");
List<String> collect = strings.stream().distinct().collect(Collectors.toList());
System.out.println(collect);//[AA, BB, CC]
```

### 3. filter() : 过滤

**签名:**
```
Stream<T> filter(Predicate<? super T> predicate)
```
返回:作用是返回一个只包含满足predicate条件元素的Stream

**代码:**
```
List<Integer> integers = Arrays.asList(10, 12, 71, 63, 921, 68, 121);
List<Integer> collect = integers.stream().filter(integer -> integer > 100).collect(Collectors.toList());
System.out.println(collect);//[921, 121]
```

### 4. flatMap() : 合并

**签名:**
```
<R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)
```
作用:把几个小的list转换到一个大的list


**效果示意图:**
![](https://52lu.github.io/images/stream-flatmap.png)


**代码:**
```
// 初始化一个嵌套集合 [[1, 2], [3, 4, 5]]
ArrayList<List<Integer>> arrayLists = new ArrayList(){{
    add(Arrays.asList(1, 2));
    add(Arrays.asList(3, 4, 5));
}};
// 使用flatMap 把多个小集合合并成大集合
List<Integer> collect = arrayLists.stream().flatMap(pList -> pList.stream()).collect(Collectors.toList());
System.out.println(collect);
// 输出 [1, 2, 3, 4, 5]
```

### 5. limit() : 截取(返回前N个元素)

**签名:**
```
Stream<T> limit(long maxSize)
```
作用:对一个Stream进行截断操作，获取其前N个元素，如果原Stream中包含的元素个数小于N，那就获取其所有的元素；


**代码:**
```
// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
// 取出前三个元素
List<Integer> collect1 = integers.stream().limit(3).collect(Collectors.toList());
System.out.println(collect1);// 输出 [1, 2, 3]
// 取出前6个元素
List<Integer> collect2 = integers.stream().limit(6).collect(Collectors.toList());
System.out.println(collect2);// 输出 [1, 2, 3, 4, 5]
```


### 6. skip() : 截取(返回不包含前N个元素)

**签名:**
```
Stream<T> skip(long n)
```
作用:返回一个丢弃原Stream的前N个元素后剩下元素组成的新Stream，如果原Stream中包含的元素个数小于N，那么返回空Stream；

**代码:**
```
// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
// 取出前三个元素
List<Integer> collect1 = integers.stream().skip(3).collect(Collectors.toList());
System.out.println(collect1);// 输出 [4, 5]
// 取出前6个元素
List<Integer> collect2 = integers.stream().skip(6).collect(Collectors.toList());
System.out.println(collect2);// 输出 []
```

### 7. sorted(): 排序

**签名:**
```
Stream<T>　sorted()和Stream<T>　sorted(Comparator<? super T> comparator)。
```
作用:返回排序后的Stream；

**代码:对整型集合排序**
```
// 声明一个list
List<Integer> integers = Arrays.asList(11, 42, 23, 74, 5);
// 默认自然序排序
List<Integer> collect = integers.stream().sorted().collect(Collectors.toList());
System.out.println("自然序排序后结果:  " + collect + "");
List<Integer> collect2 = integers.stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList());
System.out.println("逆序排序后结果:  " + collect2 + "");
```

**代码:对字符串集合排序**
```
 // 声明一个list
 List<String> strings = Arrays.asList("PHP", "JAVA", "GO", "C", "PYTHON","LUA","ABC");
 List<String> collect = strings.stream().sorted().collect(Collectors.toList());
 System.out.println("自然序排序后结果 => " + collect + "");
 //自然序排序后结果 => [ABC, C, GO, JAVA, LUA, PHP, PYTHON]
 List<String> collect1 = strings.stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList());
 System.out.println("自然序逆排序后结果 => " + collect1 + "");
 //自然序逆排序后结果 => [PYTHON, PHP, LUA, JAVA, GO, C, ABC]

 List<String> collect2 = strings.stream().sorted((str1,str2)-> str1.length()- str2.length()).collect(Collectors.toList());
 System.out.println("根据长度正序排序 => " + collect2 + "");
 // 根据长度正序排序 => [C, GO, PHP, LUA, ABC, JAVA, PYTHON]

 List<String> collect3 = strings.stream().sorted((str1,str2)-> str2.length()- str1.length()).collect(Collectors.toList());
 System.out.println("根据长度逆序排序 => " + collect3 + "");
 //根据长度逆序排序 => [PYTHON, JAVA, PHP, LUA, ABC, GO, C]
```
> <font color=orange size=4> 由上面例子，可以看出对字符串集合排序时，会发现简单使用sorted会有问题！！</font>

### 8. map(): 递归处理

**签名:**
```
<R> Stream<R> map(Function<? super T,? extends R> mapper)。
```
作用:返回一个对当前所有元素执行mapper之后的结果组成的Stream。直观的说，就是对每个元素按照某种操作进行转换，转换前后Stream中元素的个数不会改变，但元素的类型取决于转换之后的类型；

**代码**
```
// 声明一个list
List<String> strings = Arrays.asList("PHP", "JAVA", "GO", "C", "PYTHON","LUA");
List<String> collect = strings.stream().map(s -> s.toLowerCase()).collect(Collectors.toList());
System.out.println("转小写 ==> " + collect + "");
// 转小写 ==> [php, java, go, c, python, lua]

// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> collect1 = integers.stream().map(integer -> integer * 2).collect(Collectors.toList());
System.out.println("元素都乘以2 ==> " + collect1 + "");
// 元素都乘以2 ==> [2, 4, 6, 8, 10]
```

## 7.2 结束操作方法

### 1. allMatch(): 判断(都是)

**签名:**
```
boolean allMatch(Predicate<? super T> predicate)
```
作用:判断条件里的元素都满足，则返回true

**代码**
```
// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
boolean a = integers.stream().allMatch(integer -> integer > 10);
System.out.println("所有元素都大于10 ==> " + a + "");
// 所有元素都大于10 ==> false
boolean b = integers.stream().allMatch(integer -> integer < 10);
System.out.println("所有元素都小于10 ==> " + b + "");
// 所有元素都小于10 ==> true
```

### 2. anyMatch(): 判断(有一个是)

**签名:**
```
boolean anyMatch(Predicate<? super T> predicate)
```
作用:判断条件里的元素有一个满足，则返回true

**代码**
```
// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 11);
boolean a = integers.stream().anyMatch(integer -> integer > 10);
System.out.println("所有元素都大于10 ==> " + a + "");
// 所有元素都大于10 ==> true
boolean b = integers.stream().anyMatch(integer -> integer < 10);
System.out.println("所有元素都小于10 ==> " + b + "");
// 所有元素都小于10 ==> true
```

### 3. noneMatch(): 判断(都不是)

**签名:**
```
boolean noneMatch(Predicate<? super T> predicate)
```
作用:跟allMatch相反，判断条件里的元素都不满足，则返回true

**代码**
```
// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 11);
boolean a = integers.stream().noneMatch(integer -> integer > 10);
System.out.println("所有元素都大于10 ==> " + a + "");
// 所有元素都大于10 ==> true
boolean b = integers.stream().noneMatch(integer -> integer < 10);
System.out.println("所有元素都小于10 ==> " + b + "");
// 所有元素都小于10 ==> false
```


### 4. count(): 统计

**签名:**
```
long count()
```
作用:统计个数

**代码**
```
// 声明一个list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 8, 9, 9, 10);
long count = integers.stream().count();
System.out.println("总个数 ==> " + count + "");
// 总个数 ==> 12

long count1 = integers.stream().filter(integer -> integer > 5).count();
System.out.println("大于5的总个数 ==> " + count1 + "");
// 大于5的总个数 ==> 7

long count2 = integers.stream().filter(integer -> integer > 5).distinct().count();
System.out.println("大于5的去重后的总个数 ==> " + count2 + "");
// 大于5的去重后的总个数 ==> 5
```


### 5. max()、mix(): 最大和最小

**签名:**
```
Optional<T> max(Comparator<? super T> comparator)

Optional<T> min(Comparator<? super T> comparator)
```
作用:返回最大和最小

**代码**
```
// 声明一个整型list
List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Integer max = integers.stream().max(Comparator.comparing(Integer::valueOf)).get();
System.out.println(" 最大整数值 ==> " + max + "");
Integer min = integers.stream().min(Comparator.comparing(Integer::valueOf)).get();
System.out.println(" 最小整数值 ==> " + min + "");
// 声明一个字符串list
List<String> strings = Arrays.asList("B", "E", "F", "G", "H","Z","A");
String maxs = strings.stream().max(Comparator.comparing(String::valueOf)).get();
System.out.println(" 最大字符串 ==> " + maxs + "");
String mins = strings.stream().min(Comparator.comparing(String::valueOf)).get();
System.out.println(" 最小字符串 ==> " + mins + "");

/**
输出:
     最大整数值 ==> 10
     最小整数值 ==> 1
     最大字符串 ==> Z
     最小字符串 ==> A
*/
```

**获取最大和最小对象**
```
public class Main {
    public static void main(String[] args) {
        List<Employee> emps = new ArrayList<Employee>();
        emps.add(new Employee("小明", 16));
        emps.add(new Employee("张三", 28));
        emps.add(new Employee("李四", 32));
        // 设置比较器，以年龄大小来比较
        Comparator<Employee> comparator = Comparator.comparing(Employee::getAge);
        Employee minObject = emps.stream().min(comparator).get();
        Employee maxObject = emps.stream().max(comparator).get();
        System.out.println("最小对象 = " + minObject);
        System.out.println("最大对象 = " + maxObject);
        /**
          输出:
              最小对象 ==>  Name:- 小明 Age:- 16
              最大对象 ==>  Name:- 李四 Age:- 32
        */ 
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

### 6. findAny() findFirst() 查找
**签名:**
```
Optional<T> findFirst()

Optional<T> findAny()
```
**作用:**
- findFirst: 返回集合的第一个对象
- findAny: 返回这个集合中，取到的任何一个对象

**@注意:**
<div style="color:#F00">
<p>1. 在串行的流中，findAny和findFirst返回的，都是第一个对象</p>
<p>2. 在并行的流中，findAny返回的是最快处理完的那个线程的数据</p>
<p>3. 在并行操作中，对数据没有顺序上的要求，那么findAny的效率会比findFirst要快的</p>
</div>

**代码**
```
// 声明一个整型list
List<Integer> integers = Arrays.asList(10, 32, 23, 14, 51, 64, 72, 81, 59, 110);
Object[] objects = integers.stream().sorted().toArray();
Integer findFirst = integers.stream().filter(integer -> integer > 15).sorted().findFirst().get();
Integer findAny = integers.stream().filter(integer -> integer > 15).sorted().findAny().get();
System.out.println("排序后 => " + Arrays.toString(objects) + "");
System.out.println("findFirst => " + findFirst + "");
System.out.println("findAny => " + findAny + "");
/**
输出:
 排序后 => [10, 14, 23, 32, 51, 59, 64, 72, 81, 110]
 findFirst => 23
 findAny => 23
*/
```

### 7. reduce() 生成一个符合的值
**签名:**

三种重写形式:
```
// 第一种
Optional<T> reduce(BinaryOperator<T> accumulator)
// 第二种
T reduce(T identity, BinaryOperator<T> accumulator)
// 第三种
<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)
```
**说明:**

<font color=green>reduce操作可以实现从一组元素中生成一个值，sum()、max()、min()、count()等都是reduce操作，将他们单独设为函数只是因为常用


**代码**
```
// 声明一个整型list
List<Integer> integers = Arrays.asList(1,2,3,4,5);
// 集合元素求和,
Integer sum = integers.stream().reduce(Integer::sum).get();
System.out.println("求和 => " + sum + "");
// 集合元素求和后，加上初始值 100
Integer sum1 = integers.stream().reduce(100, (total, item) -> total + item);
System.out.println("求和后再加100-A => " + sum1 + "");
Integer sum2 = integers.stream().reduce(100,Integer::sum);
System.out.println("求和后再加100-B => " + sum2 + "");
// 最小值
Integer min = integers.stream().reduce(Integer::min).get();
System.out.println("最小值 => " + min + "");
// 最大值
Integer max = integers.stream().reduce(Integer::max).get();
System.out.println("最大值 => " + max + "");
// 集合中的元素乘积
Integer product = integers.stream().reduce((a, b) -> a * b).get();
System.out.println("相乘结果: => " + product + "");

// 找出最长的单词
Stream<String> stream = Stream.of("go", "php", "Java");
String lengthStr = stream.reduce((s1, s2) -> s1.length() >= s2.length() ? s1 : s2).get();
System.out.println("最长的单词: => " + lengthStr + "");

/**
输出:
    求和 => 15
    求和后再加100-A => 115
    求和后再加100-B => 115
    最小值 => 1
    最大值 => 5
    相乘结果: => 120
    最长的单词: => Java
*/
```



### 8. forEach() 遍历
**签名:**
```
void forEach(Consumer<? super E> action)
```
**作用:**是对容器中的每个元素执行action指定的动作，也就是对元素进行遍历

**代码**
```
// 声明一个Map
HashMap<String,Integer> gradeMap = new HashMap(4) {{
    put("张三", 82);
    put("李四", 70);
    put("小明", 91);
    put("老王", 59);
}};
// 遍历map
gradeMap.entrySet().stream().forEach(item-> {
    System.out.println("name = " + item.getKey()  + ", fraction = [ " + item.getValue() + " ]");
});
// 遍历集合
List<String> strings = Arrays.asList("张三", "李四", "小明", "老王");
strings.stream().forEach(s -> System.out.println("name = [ " + s + " ]"));
```


### 9. forEachOrdered() 遍历
**签名:**
```
void forEachOrdered(Consumer<? super T> action)
```
**作用:**是对容器中的每个元素执行action指定的动作，也就是对元素进行遍历

**@注意:**
<div style="color:#F00">
<p>1. 在串行的流中，forEach和forEachOrdered遍历输出的结果一样。</p>
<p>2. 在并行的流中，forEach每次输出的结果不一致。</p>
<p>3. 在并行的流中，forEachOrdered每次输出的结果一致。</p>
</div>

**代码**
```java
// 在串行流中: 遍历集合
List<Integer> strings = Arrays.asList(1, 2, 3, 4, 5);
strings.stream().forEach(s -> System.out.println("串行流-forEach =>  " + s + " "));
strings.stream().forEachOrdered(s -> System.out.println("串行流-forEachOrdered =>  " + s + " "));
// 在并行流中: 遍历集合
strings.parallelStream().forEach(s -> System.out.println("并行流-forEach =>  " + s + " "));
strings.parallelStream().forEachOrdered(s -> System.out.println("并行流-forEachOrdered =>  " + s + " "));

/**
第一次遍历
输出:
    串行流-forEach =>  1 
    串行流-forEach =>  2 
    串行流-forEach =>  3 
    串行流-forEach =>  4 
    串行流-forEach =>  5 
    串行流-forEachOrdered =>  1 
    串行流-forEachOrdered =>  2 
    串行流-forEachOrdered =>  3 
    串行流-forEachOrdered =>  4 
    串行流-forEachOrdered =>  5 
    并行流-forEach =>  3 
    并行流-forEach =>  5 
    并行流-forEach =>  4 
    并行流-forEach =>  2 
    并行流-forEach =>  1 
    并行流-forEachOrdered =>  1 
    并行流-forEachOrdered =>  2 
    并行流-forEachOrdered =>  3 
    并行流-forEachOrdered =>  4 
    并行流-forEachOrdered =>  5 
    
第二次遍历
输出:
    串行流-forEach =>  1 
    串行流-forEach =>  2 
    串行流-forEach =>  3 
    串行流-forEach =>  4 
    串行流-forEach =>  5 
    串行流-forEachOrdered =>  1 
    串行流-forEachOrdered =>  2 
    串行流-forEachOrdered =>  3 
    串行流-forEachOrdered =>  4 
    串行流-forEachOrdered =>  5 
    并行流-forEach =>  2 
    并行流-forEach =>  1 
    并行流-forEach =>  3 
    并行流-forEach =>  4 
    并行流-forEach =>  5 
    并行流-forEachOrdered =>  1 
    并行流-forEachOrdered =>  2 
    并行流-forEachOrdered =>  3 
    并行流-forEachOrdered =>  4 
    并行流-forEachOrdered =>  5 
*/
```
[Java8 parallelStream浅析-知乎](https://zhuanlan.zhihu.com/p/43039062)


### 10. toArray() 转成数组
**签名:**
```
<A> A[] toArray(IntFunction<A[]> generator)
```
**作用:** 将流转成数组


**代码**
```java
List<Integer> integerList = Arrays.asList(1, 2, 3,4,5,6,7,8,9);
Integer[] integers = integerList.stream().filter(integer -> integer % 3 == 0).toArray(Integer[]::new);
System.out.println("整型集合过滤后的结果，toArray -> " + Arrays.toString(integers) + "");
// 整型集合过滤后的结果，toArray -> [3, 6, 9]
List<String> stringsList = Arrays.asList("PHP", "GO", "JAVA", "PYTHON", "LUA");
String[] strings = stringsList.stream().filter(s -> s.length() > 3).toArray(String[]::new);
System.out.println("字符串集合过滤后的结果，toArray -> " + Arrays.toString(strings) + "");
// 字符串集合过滤后的结果，toArray -> [JAVA, PYTHON]
```
### 11. collect()

由于collect过于特殊和强大，拉出去单讲【collect() 和 Collectors(收集器) 】。

[Java-基础篇-Collect方法详解](https://52lu.github.io/2019/12/16/java-16-Stream-collect/)