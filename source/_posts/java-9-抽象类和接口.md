---
title: 学Java笔记-抽象类和接口
date: 2019-11-05 19:11:45
tags:
 - Java
categories:
 - 后端编程
---

# 1.抽象类
## 1.1 抽象类概念
抽象方法是只声明而未实现的方法，所有的抽象方法必须使用abstract关键字声明，包含抽象方法的类也必须使用abstract class声明。

### 1.1.1 抽象类定义规则
1. 抽象类和抽象方法都必须用abstract关键字来修饰；
2. 抽象类不能直接实例化，即不能使用new关键字去产生对象；
3. 抽象类定义时抽象方法只需声明，而不需实现；
4. 含有抽象方法的类必须被声明为抽象类，<font color=red>抽象类的子类必须覆写所有的抽象方法后才能被实例化，否则这个子类还是个抽象类。</font>

## 1.2 抽象类声明
```
访问权限  abstract class 类名 {
    // 声明成员变量
    访问权限 变量类型 成员变量名;
    // 声明一般方法
    访问权限 返回类型 方法名(){
        // ...
    }
    // 声明抽象方法
    abstract 返回类型 方法名();
}
```

**实例**
```
/**
 * description:
 *
 * @author : Mr.Liuqh
 * @since : 2019-10-31 18:51
 */
public abstract class People {
    // 声明成员变量
    private String name;

    // 声明一般方法
    public String getName(){
        return this.name;
    }

    // 声明抽象方法
    abstract String like();
}
```
<font color=green>由上可知:抽象类的定义就是比普通类多了一些抽象方法的定义而已。虽然定义了抽象类，但是抽象类却不能直接使用。</font>

> <font color=red> People girl = new People(); // 这种调用会报错,People 是抽象的，无法实例化</font>

### 1.2.1 抽象类的使用原则
如果说一个类的对象可以被实例化，那么就表示这个对象可以调用类中的属性或者是方法，但是抽象类中存在抽象方法，而抽象方法没有方法体，没有方法体的方法无法使用。

对于抽象类的使用原则如下:
- 抽象类必须有子类，子类使用extends继承抽象类，一个子类只能够继承一个抽象类；
- 子类（如果不是抽象类）则必须覆写抽象类之中的全部抽象方法；
- 若想实例化抽象类的对象，则可以使用子类进行对象的向上转型来完成。

## 1.3 抽象类特征
### 1.3.1 抽象类中可以有构造方法
与一般类相同，在抽象类中也可以拥有构造方法，<font color=red>但是这些构造方法必须在子类中被调用，并且子类实例化对象的时候依然满足类继承的关系，</font>先默认调用父类的构造方法，而后再调用子类的构造方法，毕竟抽象类之中还是存在属性的，只不过这个抽象方法无法直接被外部实例化对象的时候所使用。

**代码示例:**
```
//定义一抽象类Person
abstract class Person
{
    String name;
    int age;
    String occupation;
    public Person(String name,int age,String occupation) //定义构造函数
    {
        this.name=name;
        this.age=age;
        this.occupation=occupation;
    }
    public abstract String talk();
    //声明一个抽象方法
}

//声明抽象类的子类
class Student extends Person    
{
	public Student(String name,int age,String occupation){
		//在这里必须明确调用抽象类中的构造方法
		super(name,age,occupation);
	}
	
	//覆写talk()方法
	public String talk() {
		return "学生——>姓名："+this.name+"，年龄："+this.age+"，职业："+this.occupation+"！";
	}
}

// 测试
class AbstractConstructor
{
	public static void main(String[] args){
		//创建对象s
		Student s=new Student("张三",18,"学生");
	
		//调用被覆写过的方法
		System.out.println(s.talk()); // 输出:学生——>姓名：张三，年龄：18，职业：学生！ 
	
	}
}
```
> 从程序中可以看到，抽象类也可以像普通类一样，有构造方法、一般方法和属性，更重要的是还可以有一些抽象方法，需要子类去实现，而且在抽象类中声明构造方法后，在子类中必须明确调用。


### 1.3.2 抽象类不能够使用final定义。
使用final定义的类不能有子类，而抽象类使用的时候必须有子类，这是一个矛盾的问题，所以抽象类上不能出现final定义。

### 1.3.3 在外部抽象类上无法使用static声明
在外部抽象类上无法使用static声明，但是内部抽象类却可以使用static定义，使用static定义的内部抽象类就表示一个外部类。

**代码**
```java
abstract class Book{
    //抽象方法
    public abstract void print();
    //静态内部抽象类
    static abstract class CD{
      //抽象方法
      public abstract void get();         
           
    }
}
//继承抽象类
class JavaCD extends Book.CD {
    public void get(){
        System.out.println("java学习");
    }
}

public class StaticInnerAbstractClass{
    public static void main(String[]  args){
        //实例化对象
        Book.CD cd=new JavaCD();
        cd.get(); // 输出:java学习
    }
}
```
# 2. 接口
接口（interface）是Java所提供的另一种重要技术，是一种特殊的类，它的结构和抽象类非常相似，也具有数据成员与抽象方法，但它与抽象类又有不同，并且Java 8中又添加了新特性。

## 2.1 接口的基本概念
- 接口里的数据成员必须初始化，且数据成员均为常量，常见的是全局变量。
- 接口里的方法为abstract，接口不能像抽象类一样定义一般的方法，需定义“抽象方法”。

> <font color=orange>Java8中为避免在接口中添加新方法后要修改所有实现类，允许定义默认方法，即default方法，也可以称为Defender方法，或者虚拟扩展方法（Virtual extension methods)。
Default方法是指，在接口内部包含了一些默认的方法实现（也就是接口中可以包含方法体，这打破了Java之前版本对接口的语法限制），从而使得接口在进行扩展的时候，不会破坏与接口相关的实现类代码。</font>

## 2.2 接口定义
声明格式:
```
public interface 接口名称 {
    //数据成员必须赋初值
    final 数据类型 成员名称=常量；
    //抽象方法，注意没有定义方法主体
    abstract 返回数据类型 方法名称（参数…）；
    
    //默认方法，包含方法体
    default 返回值的数据类型方法名称（参数…）{
        // 方法体...
    }
}
```

**示例:**

- 无默认方法的接口

```java
interface MyInterface{
    // 数据成员必须赋初值
    static final String NAME="Mr.Liu" 
    // 抽象方法
    abstract String getName();
}
```

- 有默认方法的接口

```java
public interface MyInterface{
    // 数据成员必须赋初值
    static final String NAME="Mr.Liu" 
    // 抽象方法
    abstract String getName();
    // 默认方法
    default  String say(){
        System.out.println("Hello!")
    }
}
```

## 2.3 接口使用原则
- 接口必须有子类，子类依靠implements关键字可以同时实现多个接口；
- 接口的子类（如果不是抽象类）则必须覆写接口之中的全部抽象方法；

- 接口可以利用对象多态性，利用子类实现对象的实例化。
- 接口与一般类一样，本身也具有数据成员与方法，但数据成员一定要赋初值，且此值不能再更改
- 接口中除default方法外必须都是抽象方法，所以接口定义格式中，抽象方法声明的关键字abstract是可以省略的。

- 一个类实现多个接口时，若接口有默认方法，不能出现同名的默认方法
- 接口可以继承多个接口


# 3.抽象类和接口的区别

![](https://mrliuqh.github.io/directionsImg/java/abstractInterface.jpeg)
<!--![](https://mrliuqh.github.io/directionsImg/java/hascode-rule.jpg)-->
## 3.1 共同点
- 都是抽象类型

- 都可以有实现方法（以前接口不行）
- 都可以不需要实现类或者继承者去实现所有方法。（以前不行，现在接口中默认方法不需要实现者实现）

## 3.2 不同点
- 抽象类不可以多重继承，接口可以（无论是多重类型继承还是多重行为继承）

- 抽象类和接口所反映出的设计理念不同。其实抽象类表示的是"is-a"关系，接口表示的是"like-a"关系

- 接口中定义的变量默认是public static final 型，且必须给其初值，所以实现类中不能重新定义，也不能改变其值；抽象类中的变量默认是 friendly 型，其值可以在子类中重新定义，也可以重新赋值
> 如果一个类、类属变量及方法不以这三种修饰符来修饰，它就是friendly类型的，那么包内的任何类都可以访问它，而包外的任何类都不能访问它(包括包外继承了此类的子类)

总体来说，抽象类和接口在很大程度上都是可以互相替换使用的，但就是由于抽象类本身具备单继承局限，所以当抽象类和接口全部都可以使用的时候优先考虑接口，因为接口没有单继承局限，并且在Java8中接口可以设定默认方法，在一定程度上避免代码重复，利于后期的维护。