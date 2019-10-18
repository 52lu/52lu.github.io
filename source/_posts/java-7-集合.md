---
title: java-7-集合
date: 2019-10-18 14:07:42
tags:
 - Java
categories:
 - 后端
---
## 1.集合的作用
为了保存数量不确定的数据，以及保存具有映射关系的数据（也被称为关联数组），Java提供了集合类。集合类主要负责保存、盛装其他数据，因此集合类也被称为容器类。所有的集合类都位于java.util包下，后来为了处理多线程环境下的并发安全问题，Java 5还在java.util.concurrent包下提供了一些多线程支持的集合类。
## 2.集合分类
Java集合大致可分为Set、List和Map三种体系，
- Set: 代表无序、不可重复的集合；
- List: 代表有序、重复的集合；而
- Map: 代表具有映射关系的集合。
- `Queue: 从Java 5以后，Java又增加了Queue体系集合，代表一种队列集合实现`

## 3.集合和数组的区别
集合类和数组不一样，数组元素既可以是基本类型的值，也可以是对象（实际上保存的是对象的引用变量）；而集合里只能保存对象（实际上只是保存对象的引用变量，但通常习惯上认为集合里保存的是对象）。

**1. 存储元素角度:**
- 数组: 可以保存基本类型的值和是`对象`。
- 集合: 只能保存`对象`。

> <font color=red>数组和集合中保存对象，实际上只是保存对象的引用变量</font>

**2. 长度角度:**
数组的长度是固定的，集合的长度是可变的。

## 4.集合分类的关系
<font color=green>Java的集合类主要由两个接口派生而出：Collection和Map</font>，Collection和Map是Java集合框架的根接口，这两个接口又包含了一些子接口或实现类。

**Collection接口、子接口及其实现类的继承树**
![](https://mrliuqh.github.io/directionsImg/java/collection-extends-tree.jpeg)
`粗线圈出的Set和List接口是Collection接口派生的两个子接口，它们分别代表了无序集合和有序集合；Queue是Java提供的队列实现，有点类似于List`


**Map接口、子接口及其实现类的继承树**
![](https://mrliuqh.github.io/directionsImg/java/map-tree.jpg)


## 5.集合读存
### 5.1 存储
我们可以把Java的所有集合分成三大类，
- Set集合类似于一个罐子，把一个对象添加到Set集合时，Set集合无法记住添加这个元素的顺序，所以Set里的元素不能重复（否则系统无法准确识别这个元素）；
- List集合非常像一个数组，它可以记住每次添加元素的顺序，只是List的长度可变。
- Map集合也像一个罐子，只是它里面的每项数据都由两个值组成。

**三种集合示意图:**
![](https://mrliuqh.github.io/directionsImg/java/conllection-save.jpg)

### 5.2 读取
- List集合中的元素，可以直接根据元素的索引来访问；
- Map集合中的元素，可以根据每项元素的key来访问其value；
- Set集合中的元素，可以根据元素本身来访问（这也是Set集合里元素不允许重复的原因）

## 6.常用集合类及继承关系
![](https://mrliuqh.github.io/directionsImg/java/offen-use-conllection.png)



## 7.collection接口
Collection接口是List、Set和Queue接口的父接口，该接口里定义的方法既可用于操作Set集合，也可用于操作List和Queue集合。Collection接口里定义了如下操作集合元素的方法

### 7.1 操作集合方法列表
Collection接口操作集合元素的方法
- boolean add(Object o)：该方法用于向集合里添加一个元素。如果集合对象被添加操作改变了，则返回true。
```
public static void TestAdd(){
  // 定义一个list集合
  ArrayList<String> myList = new ArrayList<>();
  boolean a1 = myList.add("第1个元素");
  boolean a2 = myList.add("第2个元素");
  System.out.println(myList);
  System.out.println(a1);
  System.out.println(a2);
}

/* 输出:
[第1个元素, 第2个元素]
true
true
*/
```
- boolean addAll(Collection c)：该方法把集合c里的所有元素添加到指定集合里。如果集合对象被添加操作改变了，则返回true。
```
public static void TestAddAll(){
  ArrayList<String> strList = new ArrayList<>();
  strList.add("A");
  strList.add("B");
  Collection<String> subStrList = new ArrayList<>();
  subStrList.add("C");
  subStrList.add("D");
  System.out.println("合并前的strList:" + strList);
  strList.addAll(subStrList);
  System.out.println("合并后的strList:" + strList);
}

/* 输出:
合并前的strList:[A, B]
合并后的strList:[A, B, C, D]
*/
```
- void clear()：清除集合里的所有元素，将集合长度变为0。
```
public static void TestClear(){
  ArrayList<String> strList = new ArrayList<>();
  strList.add("A");
  strList.add("B");
  strList.add("C");
  strList.add("D");
  System.out.println("Clear前的strList:" + strList);
  strList.clear();
  System.out.println("Clear后的strList:" + strList);
}

/* 输出:
Clear前的strList:[A, B, C, D]
Clear后的strList:[]
*/
```
- boolean contains(Object o)：返回集合里是否包含指定元素。


- boolean containsAll(Collection c)：返回集合里是否包含集合c里的所有元素。
- boolean isEmpty()：返回集合是否为空。当集合长度为0时返回true，否则返回false。
- Iterator iterator()：返回一个Iterator对象，用于遍历集合里的元素。
- boolean remove(Object o)：删除集合中的指定元素o，当集合中包含了一个或多个元素o时，这些元素将被删除，该方法将返回true。
- boolean removeAll(Collection c)：从集合中删除集合c里包含的所有元素（相当于用调用该方法的集合减集合c），如果删除了一个或一个以上的元素，则该方法返回true。
- boolean retainAll(Collection c)：从集合中删除集合c里不包含的元素（相当于把调用该方法的集合变成该集合和集合c的交集），如果该操作改变了调用该方法的集合，则该方法返回true。

- int size()：该方法返回集合里元素的个数。
- Object[] toArray()：该方法把集合转换成一个数组，所有的集合元素变成对应的数组元素。

### 7.2 foreach 遍历集合元素
```
public static void TestForeach(){
    ArrayList<String> strList = new ArrayList<>();
    strList.add("A");
    strList.add("B");
    strList.add("C");
    strList.add("D");
    for (String str: strList){
      System.out.println(str);
    }
}


/**
输出:
A
B
C
D
**/
```
> foreach循环中的迭代变量不是集合元素本身，系统只是依次把集合元素的值赋给迭代变量，因此在foreach循环中修改迭代变量的值没有任何实际意义。
同样，<font color=red>当使用foreach循环迭代访问集合元素时，该集合不能被改变，否则将引发Concurrent ModificationException异常</font>。

**下面示例会报错:**
```
 public static void TestForeach() {
   Collection<String> strList = new ArrayList<>();
   strList.add("A");
   strList.add("B");
   strList.add("C");
   strList.add("D");
   for (String str : strList) {
       if (str.equals("A")) {
           // 这一行代码会报错
           strList.remove(str);
       }
   }
   System.out.println(strList);
}

/**报错:**
Exception in thread "main" java.util.ConcurrentModificationException..
*/
```
> <font color=ff6a00>上面程序如果改为删除“C”字符串，则不会引发异常，这样可能有些读者会“心存侥幸”地想：在迭代时好像也可以删除集合元素啊。实际上这是一种危险的行为：对于HashSet以及后面的ArrayList等，迭代时删除元素都会导致异常——只有在删除集合中的某个特定元素时才不会抛出异常，这是由集合类的实现代码决定的，程序员不应该这么做。</font>

## 8. Set集合
Set集合不允许包含相同的元素，如果试图把两个相同的元素加入同一个Set集合中，则添加操作失败，add方法返回false，且新元素不会被加入。
<font color=green>Set判断两个对象相同不是使用==运算符，而是根据equals方法。也就是说，只要两个对象用equals方法比较返回true，Set就不会接受这两个对象；反之，只要两个对象用equals方法比较返回false，Set就会接受这两个对象（甚至这两个对象是同一个对象，Set也可把它们当成两个对象处理。</font>

### 8.1 HashSet类
HashSet是Set接口的典型实现，大多数时候使用Set集合时就是使用这个实现类。HashSet按Hash算法来存储集合中的元素，因此具有很好的存取和查找性能。

#### 8.1.1 HashSet特点
- 不能保证元素的排列顺序，顺序有可能发生变化。
- HashSet不是同步的，如果多个线程同时访问一个HashSet，假设有两个或者两个以上线程同时修改了HashSet集合时，则必须通过代码来保证其同步。
- 集合元素值可以是null。

`当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该HashCode值决定该对象在HashSet中的存储位置。如果有两个元素通过equals()方法比较返回true，但它们的hashCode()方法返回值不相等，HashSet将会把它们存储在不同的位置，依然可以添加成功。`

>简单地说，HashSet集合判断两个元素相等的标准是两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法返回值也相等。

#### 8.1.2 重写hasCode的规则
HashSet中每个能存储元素的“槽位”（slot）通常称为“桶”（bucket），如果有多个元素的hashCode值相同，但它们通过equals()方法比较返回false，就需要在一个“桶”里放多个元素，这样会导致性能下降。

**重写hashCode()方法的基本规则。**
- 在程序运行过程中，同一个对象多次调用hashCode()方法应该返回相同的值。

- 当两个对象通过equals()方法比较返回true时，这两个对象的hashCode()方法应返回相等的值。
- 对象中用作equals()方法比较标准的Field，都应该用来计算hashCode值。

下面给出重写hashCode()方法的一般规则。
 - 第一步:把对象内每个有意义的Field（即每个用做equals()方法比较标准的Field）计算出一个int类型的hashCode值。计算方式如下表:
![](https://mrliuqh.github.io/directionsImg/java/hascode-rule.jpg)

  **例如如下代码:**
```
return f1.hashCode() + (int)f2;
```
 - 第二步:为了避免直接相加产生偶然相等（两个对象的f1、f2 Field并不相等，但它们的和恰好相等），可以通过为各Field乘以任意一个质数后再相加。例如如下代码：
 ```
 return f1.hashCode() * 17 + (int)f2 * 13;
 ```
 
### 8.2 LinkedHashSet类
HashSet还有一个子类LinkedHashSet，LinkedHashSet集合也是根据元素的hashCode值来决定元素的存储位置，但它同时使用链表维护元素的次序，这样使得元素看起来是以插入的顺序保存的。也就是说，当遍历LinkedHashSet集合里的元素时，LinkedHashSet将会按元素的添加顺序来访问集合里的元素。
LinkedHashSet需要维护元素的插入顺序，因此性能略低于HashSet的性能，但在迭代访问Set里的全部元素时将有很好的性能，因为它以链表来维护内部顺序。
```
public class LinkedHashSetTest
{
   public static void main(String[] args)
   {
      LinkedHashSet books=new LinkedHashSet();
      books.add("Java");
      books.add("PHP");
      System.out.println(books);
      //删除 Java
      books.remove("Java");
      //重新添加 Java
      books.add("Java");
      System.out.println(books);
   }
}

/*输出:

[Java,PHP]
[PHP,Java]
*/
```

> 输出LinkedHashSet集合的元素时，元素的顺序总是与添加顺序一致。

<font color=green>虽然LinkedHashSet使用了链表记录集合元素的添加顺序，但LinkedHashSet依然是HashSet，因此它依然不允许集合元素重复。</font>

### 8.3 TreeSet类
TreeSet是SortedSet接口的实现类，正如SortedSet名字所暗示的，<font color=ff6a00>TreeSet可以确保集合元素处于排序状态。</font>与HashSet集合相比，TreeSet还提供了如下几个额外的方法。
#### 8.3.1 TreeSet专有的方法
- Comparator comparator()：如果TreeSet采用了定制排序，则该方法返回定制排序所使用的Comparator；如果TreeSet采用了自然排序，则返回null。

- Object first()：返回集合中的第一个元素。
- Object last()：返回集合中的最后一个元素。
- Object lower(Object e)：返回集合中位于指定元素之前的元素（即小于指定元素的最大元素，参考元素不需要是TreeSet集合里的元素）。
- Object higher (Object e)：返回集合中位于指定元素之后的元素（即大于指定元素的最小元素，参考元素不需要是TreeSet集合里的元素）。
- SortedSet subSet(fromElement, toElement)：返回此Set的子集合，范围从fromElement（包含）到toElement（不包含）。
- SortedSet headSet(toElement)：返回此Set的子集，由小于toElement的元素组成。
- SortedSet tailSet(fromElement)：返回此Set的子集，由大于或等于fromElement的元素组成。

**使用**
```
TreeSet treeSet = new TreeSet();
treeSet.add(1);
treeSet.add(2);
```

**<font color=fc7900>@注意:</font>**
- TreeSet并不是根据元素的插入顺序进行排序的，而是根据元素的实际值的大小进行排序的。

- TreeSet是采用红黑树的数据结构来存储集合元素。
- TreeSet支持两种排序方法:自然排序和定制排序，默认采用自然排序
- TreeSet的集合，只能存放实现Comparable接口的类的对象（java一些常用的类已经实现了Comparable接口）。

### 8.4 EnumSet类
EnumSet是一个专为枚举类设计的集合类，EnumSet中的所有元素都必须是指定枚举类型的枚举值，该枚举类型在创建EnumSet时显式或隐式地指定。EnumSet的集合元素也是有序的，EnumSet以枚举值在Enum类内的定义顺序来决定集合元素的顺序。

**<font color=fc7900>@注意:</font>**
- EnumSet的集合元素也是有序。
- EnumSet在内部以位向量的形式存储，这种存储形式非常紧凑、高效，因此EnumSet对象占用内存很小，而且运行效率很好。
- 批量操作（如调用containsAll和retainAll方法）时，如果其参数也是EnumSet集合，则该批量操作的执行速度也非常快。
- EnumSet集合不允许加入null元素，否则抛出NullPointerException异常
- EnumSet类没有暴露任何构造器来创建该类的实例，程序应该通过它提供的static方法来创建EnumSet对象。

#### 8.4.1 创建EnumSet对象的静态方法
- static EnumSet allOf(Class elementType)：创建一个包含指定枚举类里所有枚举值的EnumSet集合。

- static EnumSet complementOf(EnumSet s)：创建一个其元素类型与指定EnumSet里元素类型相同的EnumSet集合，新EnumSet集合包含原EnumSet集合所不包含的、此枚举类剩下的枚举值（即新EnumSet集合和原EnumSet集合的集合元素加起来就是该枚举类的所有枚举值）。
- static EnumSet copyOf(Collection c)：使用一个普通集合来创建EnumSet集合。
- static EnumSet copyOf(EnumSet s)：创建一个与指定EnumSet具有相同元素类型、相同集合元素的EnumSet集合。
- static EnumSet noneOf(Class elementType)：创建一个元素类型为指定枚举类型的空EnumSet。
- static EnumSet of(E first, E... rest)：创建一个包含一个或多个枚举值的EnumSet集合，传入的多个枚举值必须属于同一个枚举类。
- static EnumSet range(E from, E to)：创建一个包含从from枚举值到to枚举值范围内所有枚举值的EnumSet集合。


**下面程序示范了如何使用EnumSet来保存枚举类的多个枚举值**
```
enum Season
{
    SPRING,SUMMER,FALL,WINTER
}
public class EnumSetTest
{
    public static void main(String[] args)
    {
       //创建一个EnumSet集合，集合元素就是Season枚举类的全部枚举值
       EnumSet es1=EnumSet.allOf(Season.class);
       //输出[SPRING,SUMMER,FALL,WINTER]
       System.out.println(es1);
       //创建一个EnumSet空集合，指定其集合元素是Season类的枚举值
       EnumSet es2=EnumSet.noneOf(Season.class);
       //输出[]
       System.out.println(es2);
       //手动添加两个元素
       es2.add(Season.WINTER);
       es2.add(Season.SPRING);
       //输出[SPRING,WINTER]
       System.out.println(es2);
       //以指定枚举值创建EnumSet集合
       EnumSet es3=EnumSet.of(Season.SUMMER , Season.WINTER);
       //输出[SUMMER,WINTER]
       System.out.println(es3);
       EnumSet es4=EnumSet.range(Season.SUMMER , Season.WINTER);
       //输出[SUMMER,FALL,WINTER]
       System.out.println(es4);
       //新创建的EnumSet集合元素和es4集合元素有相同的类型
       /es5集合元素 + es4集合元素=Season枚举类的全部枚举值
       EnumSet es5=EnumSet.complementOf(es4);
       //输出[SPRING]
       System.out.println(es5);
    }
}
```
### 8.5 各Set实现类的性能分析
HashSet和TreeSet是Set的两个典型实现，到底如何选择HashSet和TreeSet呢？HashSet的性能总是比TreeSet好（特别是最常用的添加、查询元素等操作），因为TreeSet需要额外的红黑树算法来维护集合元素的次序。只有当需要一个保持排序的Set时，才应该使用TreeSet，否则都应该使用HashSet。

HashSet还有一个子类：LinkedHashSet，对于普通的插入、删除操作，LinkedHashSet比HashSet要略微慢一点，这是由维护链表所带来的额外开销造成的；不过，因为有了链表，遍历LinkedHashSet会更快。

EnumSet是所有Set实现类中性能最好的，但它只能保存同一个枚举类的枚举值作为集合元素。

必须指出的是，Set的三个实现类HashSet、TreeSet和EnumSet都是线程不安全的。如果有多个线程同时访问一个Set集合，并且有超过一个线程修改了该Set集合，则必须手动保证该Set集合的同步性。通常可以通过Collections工具类的synchronizedSortedSet方法来“包装”该Set集合。此操作最好在创建时进行，以防止对Set集合的意外非同步访问。例如：
```
SortedSet s=Collections.synchronizedSortedSet(new TreeSet(...));
```

## 9.List集合
List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引。List集合允许使用重复元素，可以通过索引来访问指定位置的集合元素。List集合默认按元素的添加顺序设置元素的索引，

### 9.1 List接口和ListIterator接口
List作为Collection接口的子接口，当然可以使用Collection接口里的全部方法。而且由于List是有序集合，因此List集合里增加了一些根据索引来操作集合元素的方法。
<!--<div style="background:#b4d068">-->

- void add(int index, Object element): 将元素element插入到List集合的index处。

- boolean addAll(int index, Collection c): 将集合c所包含的所有元素都插入到List集合的index处。
- Object get(int index): 返回集合index索引处的元素。
- int indexOf(Object o): 返回对象o在List集合中第一次出现的位置索引。
- int lastIndexOf(Object o): 返回对象o在List集合中最后一次出现的位置索引。
- Object remove(int index): 删除并返回index索引处的元素。
- Object set(int index, Object element): 将index索引处的元素替换成element对象，返回新元素。
- List subList(int fromIndex, int toIndex): 返回从索引fromIndex（包含）到索引toIndex（不包含）处所有集合元素组成的子集合。

> 所有的List实现类都可以调用这些方法来操作集合元素。与Set集合相比，List增加了根据索引来插入、替换和删除集合元素的方法。


### 9.2 ArrayList和Vector实现类
ArrayList和Vector作为List类的两个典型实现，完全支持前面介绍的List接口的全部功能。

ArrayList或Vector对象使用initialCapacity参数来设置该数组的长度，当向ArrayList或Vector中添加元素超出了该数组的长度时，它们的initialCapacity会自动增加。
对于通常的编程场景，程序员无须关心ArrayList或Vector的initialCapacity。<font color=#ffa500>但如果向ArrayList或Vector集合中添加大量元素时，可使用ensureCapacity(int minCapacity)方法一次性地增加initialCapacity。这可以减少重分配的次数，从而提高性能。</font>

**创建空的ArrayList或Vector集合时不指定initialCapacity参数，则Object[]数组的长度默认为10。**

#### 9.2.1 重新分配ArrayList和Vector长度方法
-  void ensureCapacity(int minCapacity)：将ArrayList或Vector集合的Object[]数组长度增加minCapacity。

-  void trimToSize()：调整ArrayList或Vector集合的Object[]数组长度为当前元素的个数。程序可调用该方法来减少ArrayList或Vector集合对象占用的存储空间。

#### 9.2.2 ArrayList和Vector的区别

- ArrayList是线程不安全的: 当多个线程访问同一个ArrayList集合时，如果有超过一个线程修改了ArrayList集合，则程序必须手动保证该集合的同步性
- Vector集合则是线程安全的:因为Vector是线程安全的，所以Vector的性能比ArrayList的性能要低

> 实际上，即使需要保证List集合线程安全，也同样不推荐使用Vector实现类。Collections工具类可以将一个ArrayList变成线程安全的。



#### 9.2.3 固定长度的List
操作数组的工具类：Arrays，供了asList(Object... a)方法，该方法可以把一个数组或指定个数的对象转换成一个List集合，这个List集合既不是ArrayList实现类的实例，也不是Vector实现类的实例，而是Arrays的内部类ArrayList的实例。

<font color=red>Arrays.ArrayList是一个固定长度的List集合，程序只能遍历访问该集合里的元素，不可增加、删除该集合里的元素。</font>

**如下程序所示:**
```
public class FixedSizeList
{
    public static void main(String[] args)
    {
          List fixedList=Arrays.asList("Java"
                , "PHP");
          //获取fixedList的实现类，将输出Arrays$ArrayList
          System.out.println(fixedList.getClass());
          //遍历fixedList的集合元素
          for (int i=0; i < fixedList.size() ; i++)
          {
              System.out.println(fixedList.get(i));
          }
          //试图增加、删除元素都会引发UnsupportedOperationException异常
          fixedList.add("Go"); // <1>
          fixedList.remove("Python"); // <2>
    }
}

/*输出:
Java
PHP
Exception in thread "main" java.lang.UnsupportedOperationException...
*/
```
> 上面程序中<1> <2>两行代码对于普通的List集合完全正常，但如果试图通过这两个方法来增加、删除Arrays$ArrayList集合里的元素，将会引发异常。所以上面程序在编译时完全正常，但会在运行第一行粗体字标识的代码行处引发UnsupportedOperationException异常。

## 10.Queue集合
Queue用于模拟队列这种数据结构，队列通常是指“先进先出”（FIFO）的容器。队列的头部保存在队列中存放时间最长的元素，队列的尾部保存在队列中存放时间最短的元素。新元素插入（offer）到队列的尾部，访问元素（poll）操作会返回队列头部的元素。通常，队列不允许随机访问队列中的元素。

### 10.1 Queue接口方法
-  void add(Object e)：将指定元素加入此队列的尾部。

-  Object element()：获取队列头部的元素，但是不删除该元素。
-  boolean offer(Object e)：将指定元素加入此队列的尾部。当使用有容量限制的队列时，此方法通常比add(Object e)方法更好。
-  Object peek()：获取队列头部的元素，但是不删除该元素。如果此队列为空，则返回null。
-  Object poll()：获取队列头部的元素，并删除该元素。如果此队列为空，则返回null。
-  Object remove()：获取队列头部的元素，并删除该元素。

Queue接口有一个PriorityQueue实现类。除此之外，Queue还有一个Deque接口，Deque代表一个“双端队列”，双端队列可以同时从两端来添加、删除元素，因此Deque的实现类既可当成队列使用，也可当成栈使用。Java为Deque提供了ArrayDeque和LinkedList两个实现类。

### 10.2 PriorityQueue实现类
#### 10.2.1 特点:
- 存元素顺序:PriorityQueue保存队列元素的顺序并不是按加入队列的顺序，而是按队列元素的大小进行重新排序
- 取元素顺序:当调用peek()方法或者poll()方法取出队列中的元素时，并不是取出最先进入队列的元素，而是取出队列中最小的元素。
- PriorityQueue不允许插入null元素

> PriorityQueue不遵守队列的最基本规则:先进先出（FIFO）

#### 10.2.2 元素排序方式
-  自然排序：采用自然顺序的PriorityQueue集合中的元素必须实现了Comparable接口，而且应该是同一个类的多个实例，否则可能导致ClassCastException异常。
-  定制排序：创建PriorityQueue队列时，传入一个Comparator对象，该对象负责对队列中的所有元素进行排序。采用定制排序时不要求队列元素实现Comparable接口。

> PriorityQueue队列对元素的要求与TreeSet对元素的要求基本一致，因此关于使用自然排序和定制排序和TreeSet一样。

### 10.3 Deque接口与ArrayDeque实现类
Deque接口是Queue接口的子接口，它代表一个双端队列，Deque接口里定义了一些双端队列的方法，这些方法允许从两端来操作队列的元素。

-  void addFirst(Object e):将指定元素插入该双端队列的开头。
-  void addLast(Object e):将指定元素插入该双端队列的末尾。
-  Iterator descendingIterator():返回该双端队列对应的迭代器，该迭代器将以逆向顺序来迭代队列中的元素。
-  Object getFirst():获取但不删除双端队列的第一个元素。
-  Object getLast():获取但不删除双端队列的最后一个元素。
-  boolean offerFirst(Object e):将指定元素插入该双端队列的开头。
-  boolean offerLast(Object e):将指定元素插入该双端队列的末尾。
-  Object peekFirst():获取但不删除该双端队列的第一个元素；如果此双端队列为空，则返回null。
-  Object peekLast():获取但不删除该双端队列的最后一个元素；如果此双端队列为空，则返回null。
-  Object pollFirst():获取并删除该双端队列的第一个元素；如果此双端队列为空，则返回null。
-  Object pollLast():获取并删除该双端队列的最后一个元素；如果此双端队列为空，则返回null。
-  Object pop()（栈方法）:pop出该双端队列所表示的栈的栈顶元素。相当于removeFirst()。
-  void push(Object e)（栈方法）:将一个元素push进该双端队列所表示的栈的栈顶。相当于addFirst(e)。
-  Object removeFirst():获取并删除该双端队列的第一个元素。
-  Object removeFirstOccurrence(Object o):删除该双端队列的第一次出现的元素o。
-  removeLast():获取并删除该双端队列的最后一个元素。

-  removeLastOccurrence(Object o):删除该双端队列的最后一次出现的元素o。


> Deque不仅可以当成双端队列使用，而且可以被当成栈来使用，因为该类里还包含了pop（出栈）、push（入栈）两个方法。

Deque接口提供了一个典型的实现类：ArrayDeque，它是一个基于数组实现的双端队列，创建Deque时可指定一个numElements参数，该参数用于指定Object[]数组的长度；如果不指定numElements参数，Deque底层数组的长度为16。

**<font color=green>@提示:</font>**
> ArrayList和ArrayDeque两个集合类的实现机制基本相似，它们的底层都采用一个动态的、可重分配的Object[]数组来存储集合元素，当集合元素超出了该数组的容量时，系统会在底层重新分配一个Object[]数组来存储集合元素。


### 10.4  LinkedList实现类
LinkedList类是List接口的实现类——这意味着它是一个List集合，可以根据索引来随机访问集合中的元素。除此之外，LinkedList还实现了Deque接口，因此它可以被当成双端队列来使用，自然也可以被当成“栈”来使用了。

#### 10.4.1 LinkedList与ArrayList、ArrayDeque的实现机制区别

- ArrayList、ArrayDeque内部以数组的形式来保存集合中的元素，因此随机访问集合元素时有较好的性能；
- LinkedList内部以链表的形式来保存集合中的元素，因此随机访问集合元素时性能较差，但在插入、删除元素时性能非常出色（只需改变指针所指的地址即可）。

## 11. 各种线性表性能分析
Java提供的List就是一个线性表接口，而ArrayList、LinkedList又是线性表的两种典型实现：基于数组的线性表和基于链的线性表。Queue代表了队列，Deque代表了双端队列（既可作为队列使用，也可作为栈使用），接下来对各种实现类的性能进行分析。

LinkedList集合不仅提供了List的功能，还提供了双端队列、栈的功能。关于性能方面可看下图：
![](https://mrliuqh.github.io/directionsImg/java/collentions-comparison.jpg)

- 以数组作为底层实现的集合在随机访问时性能较好；
- 以链表作为底层实现的集合在执行插入、删除操作时有很好的性能；
- 进行迭代操作时，以链表作为底层实现的集合比以数组作为底层实现的集合性能好。