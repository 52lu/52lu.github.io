---
title: 基础篇-泛型
date: 2019-10-17 19:10:22
tags:
 - Java
categories:
 - 后端编程
---

## 1. 为什么使用泛型程序设计？
使用泛型机制编写的程序代码要比那些杂乱地使用Object变量，然后再进行强制类型转换的代码具有更好的安全性和可读性。 泛型对于集合类尤其有用。

> 泛型程序设计(Generic programming) 意味着编写的代码可以被很多不同类型的对象所 重用。 

## 2. 泛型定义
声明类的泛型版本与声明类的一般版本的语法格式很类似，只有稍微的不同。

**声明一般类的语法格式为：**

```
[修饰符] class类名 [extends父类名] [implements接口1名,接口2名,…]{
        //类体
}
```

**而声明泛型类型时，只需要在类名后面跟上一个类型参数即可，其语法格式为：**
- 单类型参数泛型类型声明

```
[修饰符] class类名<类型参数> [extends父类名] [implements接口1名,接口2名,。。。]{
       //类体
}
```

- 多类型参数泛型类型声明

```
[修饰符] class类名<类型参数1,类型参数2,类型参数3> [extends父类名] [implements接口1名,接口2名,。。。]{
       //类体
}
```
> <font color=red>每个类型参数必须唯一。</font>



**代码示例:**

- box类

```
public class Box {
    private Object object;

    public void add(Object object){
        this.object = object;
    }
    public Object get(){
        return object;
    }
}
```
- box 泛型类

```
// Box类的泛型版本.
public class Box<T> {
    private T t;        // T代表"类型"
    public void add(T t) {
      this.t = t;
    }
    public T get() {
      return t;
  }
}
```
- T:"代表类型变量"


## 3. 泛型使用

一个泛型类型调用通常被看做是“参数化类型”。要实例化这个类，照常使用new关键字，但是将`<Integer>`放在类名和圆括号之间：
```
Box<Integer> integerBox = new Box<Integer>();
integerBox.add(new Integer(10));
// 不需要类型转换
Integer someInteger = integerBox.get();
System.out.println(someInteger)
```

## 4.泛型方法
### 4.1 定义泛型方法
类型参数还可以在方法和构造器签名中声明，用来创建“泛型方法”和“泛型构造器”。这与声明一个泛型类型相似，但是类型参数的作用域被限制在它被声明的方法或构造器中。

**声明格式:**
```
[修饰符] <类型变量> [返回类型] 方法名(参数列表){
    // 方法体
}
```

**代码示例:**
```
class ArrayAlg{ 
   public static <T> T getMiddle(T... a){ 
     return a[a.length / 2];
  }
}
```
这个方法是在普通类中定义的， 而不是在泛型类中定义的。 然而， 这是一个泛型方法， 可以从尖括号和类型变量看出这一点。<font color=red>注意:类型变量放在修饰符(这里是 public static) 的后面，返回类型的前面。</font>

### 4.2 调用泛型方法
当调用一个泛型方法时’ 在方法名前的尖括号中放入具体的类型

**调用ArrayAlg类中的泛型方法:**
```
String middle = ArrayAlg.<String>getMiddle("]ohnM, "Q.n, "Public");
```

在大多数情况下,方法调用中可以省略<String>类型参数。编译器有足够的信息能够推断出所调用的方法。它用 names 的类型(即 String[]) 与泛型类型 T[ ] 进行匹配并推断出 T 一定是 String。也就是说上述代码也可以修改为:

```
String middle = ArrayAlg.getHiddle("]ohn", "Q.", "Public");
```

## 5.类型变量
### 5.1 类型参数命名惯例
按惯例，类型参数命名为单个的大写字母。这与已经知道的变量命名规则不太相同。这样命名的原因是：如果不这样命名，将很难区分在一个类型变量和一个变通类或接口名称之间的不同。最普遍使用的类型参数名称如下：
- E——元素（被Java集合框架所广泛地使用）。
- K——键。
- N——数字。
- T——类型。
- V——值。
- S，U，V等——第二、第三、第四个类型。

### 5.2 类型变量的限定
#### 5.2.1 类型参数边界
默认可以使用任何类型来实例化一个泛型类对象，但Java中也对泛型类实例的类型作了限制。

**语法如下:**

```
 <T exteds anyClass>
```

1. anyClass指某个接口或类
2. anyClass指的是上限
3. 限定上限的类型中，最多只能有1个类(或0个类)，其余的必须均为接口。
4. 限定上限的类型中，接口可以有多个，中间用&字符分割。

> 使用泛型限制后，泛型类的类型必须实现或者继承了anyClass 这个接口或类。无论anyClass是接口还是类，在进行泛型限制时都必须使用extends关键字。

- 泛型类限制使用

```
import java.util.List;
public class LimitClass<T extends List>{
    // ....
}
```

- 泛型方法限制使用

```
import java.util.List;
public class LimitClass{
    public static <T extends List> void testMedthod(T t){
        // ....
    }
}
```

对类型参数的限定，也可以包括指定额外的必须被实现的接口，使用&字符，例如指定类型变量U可接收的值为实现了MyInterface接口的Number类或其之类。
**语法如下:**

```
// 上限类型中有一个接口
<U extends Number & MyInterface>

// 上限类型中有n个接口
<U extends Number & MyInterface1 & MyInterface2 & ... &MyInterface...>
```

#### 5.2.2 使用通配符: ?
固定的泛型类型使用起来不灵活，java提供一种巧妙的方案:通配符类型。

**定义格式**

```
//不设置上限:表示任何类型
AnyGeneric<?>
AnyGeneric<?,？>

// 设置上限
AnyGeneric<? extends MyClass>
```
<font color=blue> 表示任何泛型AnyGeneric类型，它的类型参数是MyClass的子类</font>

**代码1:**
```
public class Demo {
    public static void main(String[] args){
        Point<Integer, Integer> p1 = new Point<Integer, Integer>();
        p1.setX(10);
        p1.setY(20);
        printPoint(p1);

        Point<String, String> p2 = new Point<String, String>();
        p2.setX("东经180度");
        p2.setY("北纬210度");
        printPoint(p2);
    }
    // 使用通配符
    public static void printPoint(Point<?, ?> p){  
        System.out.println("This point is: " + p.getX() + ", " + p.getY());
    }
}
class Point<T1, T2>{
    T1 x;
    T2 y;
    public T1 getX() {
        return x;
    }
    public void setX(T1 x) {
        this.x = x;
    }
    public T2 getY() {
        return y;
    }
    public void setY(T2 y) {
        this.y = y;
    }
}
/*
运行结果：
This point is: 10, 20
This point is: 东经180度, 北纬210度
*/
```

**代码2:**
```
public class Demo {
    public static void main(String[] args){
        Point<Integer, Integer> p1 = new Point<Integer, Integer>();
        p1.setX(10);
        p1.setY(20);
        printNumPoint(p1);

        Point<String, String> p2 = new Point<String, String>();
        p2.setX("东经180度");
        p2.setY("北纬210度");
        printStrPoint(p2);
    }

    // 借助通配符限制泛型的范围
    public static void printNumPoint(Point<? extends Number, ? extends Number> p){
        System.out.println("x: " + p.getX() + ", y: " + p.getY());
    }

    public static void printStrPoint(Point<? extends String, ? extends String> p){
        System.out.println("GPS: " + p.getX() + "，" + p.getY());
    }
}
class Point<T1, T2>{
    T1 x;
    T2 y;
    public T1 getX() {
        return x;
    }
    public void setX(T1 x) {
        this.x = x;
    }
    public T2 getY() {
        return y;
    }
    public void setY(T2 y) {
        this.y = y;
    }
}
/*
运行结果：
x: 10, y: 20
GPS: 东经180度，北纬210度
*/
```

## 6.类型擦除
当一个泛型被实例化时，编译器通过称为“类型擦除”的技术来编译这些类型。<font color=green>所谓类型擦除，指的是编译器移除一个类或方法中所有与类型参数相关的信息。</font>类型擦除能够使使用泛型的Java应用程序与Java类库和在泛型出现之前创建的应用程序保持二进制上的兼容性。

例如:将泛型类Pair<T> 进行类型擦除
**擦除前:**
```
public class Pair<T> {
    private T first;
    private T last;
    public Pair(T first, T last) {
        this.first = first;
        this.last = last;
    }
    public T getFirst() {
        return first;
    }
    public T getLast() {
        return last;
    }
}
```
**擦除后:**
```
public class Pair {
    private Object first;
    private Object last;
    public Pair(Object first, Object last) {
        this.first = first;
        this.last = last;
    }
    public Object getFirst() {
        return first;
    }
    public Object getLast() {
        return last;
    }
}
```
因此，Java使用擦拭法实现泛型，导致了：
- 编译器把类型<T>视为Object；
- 编译器根据<T>实现安全的强制转型。


## 7.泛型的局限

Java的泛型是由编译器在编译时实行的，编译器内部永远把所有类型T视为Object处理，但是，在需要转型的时候，编译器会根据T的类型自动为我们实行安全地强制转型。了解了Java泛型的实现方式——擦拭法，我们就知道了Java泛型的局限：

### 7.1 不能用类型参数代替基本类型
不能用类型参数代替基本类型，因此没有 Pair<int>、Pair<double>...
```
Pair<int> p = new Pair<int>(1, 2); // compile error!
```
### 7.2 无法判断带泛型的Class
```
Pair<Integer> p = new Pair<>(123, 456);
// Compile error:
if (p instanceof Pair<String>.class) {
}
```
<font color=purple>原因和前面一样，并不存在Pair<String>.class，而是只有唯一的Pair.class。</font>

### 7.3 无法实例化T类型
```
public class Pair<T> {
    private T first;
    private T last;
    public Pair() {
        // Compile error:
        first = new T();
        last = new T();
    }
}
```
上述代码无法通过编译，因为构造方法的两行语句：
```
first = new T();
last = new T();
//擦拭后实际上变成了：
first = new Object();
last = new Object();
```
### 7.4 无法创建参数化类型的数组
```
Pair<String>[] table = new Pair<String>[10]; //error 
```
需要说明的是，只是不允许创建这些数组，而声明类型为Pair<String>[]的变量仍是合法的，不过不能用new Pair<String>[10]初始化这个变量。



### 7.5 总结
Java的泛型是采用擦拭法实现的；擦拭法决定了泛型`<T>`：
- 不能是基本类型，例如：int；
- 不能获取带泛型类型的Class，例如：Pair<String>.class；
- 不能判断带泛型类型的类型，例如：x instanceof Pair<String>；
- 不能实例化T类型，例如：new T()。