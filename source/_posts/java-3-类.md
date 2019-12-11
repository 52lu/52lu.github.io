---
title: Java-基础篇-类
date: 2019-10-15 19:19:48
tags:
 - Java
categories:
 - 后端编程
---


# 1. 类
**类的定义**
```
[修饰符] class 类名 {
    // 零或多个构造器定义...

    
    // 零或多个Field...
    
    // 零或多个方法...
}
```
- 修饰符: 如public，private及许多其他修饰符
- 类名，并且按惯例首字母要大写。
- 父类（超类）的名称，都要在前面加上关键字extends。一个类只能继承自一个父类。
- 被该类实现的接口列表（用逗号进行分隔），在接口前面加上关键字implements。一个类可以实现多个接口。
- 类体，用花括号{}包围。


## 1.1 定义MyClass类
```
public class MyClass {
    /**
    * @Description: 静态常量
    */
    public final static Double PI = 3.1415;

    /**
     * @Description: 名称（成员变量）
     */
    private String name;

    /**
     * @Description: 设置名称（成员方法）
     */
    public void setName(String name) {
        this.name = name;
    }
    /**
     * @description: 获取名称（成员方法）
     **/
    public String getName() {
        return this.name;
    }
    /**
     * @Description: 无参构造方法
     */
    public MyClass() {
        System.out.println("无参构造方法....");
    }

    public static void staticMethod(){
        System.out.println("这是个静态方法");
    }
    /**
     * @description:有参构造方法
     **/
    public MyClass(String name) {
        this.name = name;
        System.out.println("有参构造方法....");
    }
}
```
## 1.2 调用MyClass类

### 1.2.1 实例化调用
```
MyClass classTest = new MyClass();
classTest.setName("小明");
String name = classTest.getName();
System.out.println(name);

/*输出:
无参构造方法....
小明
*/
```
### 1.2.2 静态调用
```
//调用静态成员
System.out.println(MyClass.PI);
//调用静态方法
MyClass.staticMethod();

/*输出:
无参构造方法....
小明
*/
```
> <font color=006400>虽然静态成员也可以使用"对象.静态成员"的形式进行调用，但通常不建议用这样的形式，因为这样容易混淆静态成员和非静态成员。</font>



- 测试有参构造方法

```
MyClass classTest2 = new MyClass("张三");
System.out.println(classTest2.getName());

/*输出:
有参构造方法....
张三
*/
```

## 1.3 类方法分析

```
[修饰符] 返回类型 方法名称(方法参数列表){
    // 方法体
} 
```
- 返回类型: 方法所返回的数据值的数据类型，若方法不返回值，用void


### 1.3.1 方法重载
Java语言支持“重载”方法，并且Java能够根据不同的“方法签名”来区分重载的方法。这意味着在一个类中，可以有相同名称但具有不同参数列表的方法（当然会有一些限定条件，这将在“接口和继承”中讨论）。


**代码示例**

```
public class DrawDate{

    //声明绘制字符串的方法
    public void draw(String s){          
        // ...
    }
    //声明绘制整数的方法
    public void draw(int i){
        // ...
    }
     //声明绘制双精度小数的方法
    public void draw(double f){
        // ...
    }
    //声明绘制一个整数和一个小数的方法
    public void draw(int i , double f){   
        // ...
    }
}
```
### 1.3.2 传递任意数量的参数

可以使用“可变参数”来传递任意数量的值到一个方法。当程序员不知道有多少个特定类型的参数要被传递给方法时，使用可变参数。可变参数是手工创建一个数组的简洁方法。
要使用可变参数，在最后一个参数的类型后面跟上省略号“…”，然后是一个空格，再接着是参数名。具有这种可变参数的方法可以接收任意数量的该类型的参数，包括空参数。例如下面代码：

```
public Polygon polygonFrom(Point... corners) {
    //corners参数被当作数组处理，可以调用数组属性length获得长度
    int numberOfSides = corners.length;
    //声明两个变量
    double squareOfSide1, lengthOfSide1;
    squareOfSide1 = (corners[1].x - corners[0].x)*(corners[1].x - corners[0].x) + (corners[1].y - corners[0].y)*(corners[1].y - corners[0].y) ;
    lengthOfSide1 = Math.sqrt(squareOfSide1); //调用Math类的静态方法求平方根
    …
}
```
> 可以看出来，在上面这个方法中，corners被当做数组使用。调用这个方法时，既可以使用一个数组，也可以使用一系列的参数。不管使用哪种形式，方法中的代码都会将参数作为一个数组对待。



## 1.4 构造器

**语法**
```
public 类名称(方法参数列表){
    // 方法体
} 
```
- 构造器与类同名 
- 每个类可以有一个以上的构造器 
- 构造器可以有 0 个、1 个或多个参数 
- 构造器没有返回值 
- 构造器总是伴随着 new 操作一起调用

### 1.4.1 无参构造器
```
    /**
    * @description:无参构造器
     **/
    public MyClass() {
        System.out.println("无参构造器....");
    }
```

### 1.4.2 有参构造器
```
    /**
     * @description:有参构造器
     **/
    public MyClass(String name) {
        this.name = name;
        System.out.println("有参构造器....");
    }
```

# 2. Object类
Object类是Java一个比较特殊的类，Java中所有的类从根本上都继承自Object类。Object是Java中唯一没有父类的类。
> 如果一个类没有使用extends关键字明确标识继承另外一个类，那么这个类就默认继承Object类

## Object类中常用的方法
![](https://52lu.github.io/directionsImg/java/object-fun1.jpg)
![](https://52lu.github.io/directionsImg/java/object-fun2.jpg)

# 3. 内部类
所谓的内部类就是指在一个类的内部又定义了其他类的情况。如果在类Outer的内部再定义一个类Inner，此时类Inner就称为内部类，而类Outer则称为外部类。内部类可声明为public或private。当内部类声明为public或private时，对其访问的限制与成员变量和成员方法完全相同。


## 3.1 定义
``` java
权限标示符 class 外部类名称{
    ...
    
    权限标示符 class 内部类名称 {
        ...
    }
}
```

**示例:**
```
// 外部类
public class Outer{
    // 外部类属性
	int score=95;
	
	// 外部类方法，调用内部类方法
	void inst(){
		Inner in=new Inner();
		in.display();
	}
	// 内部类
	public class Inner{
		//在内部类中声明name属性
		String name="张三";
		// 内部类方法
		void display(){
			//内部类访问外部类中的属性
			System.out.println("成绩:score="+score);
		}
	}
}

// 测试使用
public class ObjectInnerDemo{
	public static void main(String[]args){
		Outer outer=new Outer();
		outer.inst(); //输出:成绩:score=95
	}
}
```

## 3.2 内部类主要作用
- 内部类提供了更好的封装，可以把内部类隐藏在外部类之内，不允许同一个包中的其他类访问该类。

- 内部类成员可以直接访问外部类的私有数据，因为内部类被当成其外部类成员，同一个类的成员之间可以相互访问。<font color=red>但外部类不能访问内部类的实现细节，例如内部类的成员变量</font>。
- 匿名内部类适合用于创建那些仅需要一次的类


## 3.3 使用static定义的内部类就是外部类
如果使用static来修饰一个内部类，则这个内部类就属于外部类本身，而不属于外部类的某个对象。因此使用static修饰的内部类被称为类内部类，有的地方也称为静态内部类。


> <font color=green>static关键字的作用是把类的成员变成类相关，而不是实例相关，即static修饰的成员属于整个类，而不属于单个对象。


静态内部类可以包含静态成员，也可以包含非静态成员。根据静态成员不能访问非静态成员的规则，静态内部类不能访问外部类的实例成员，只能访问外部类的类成员。即使是静态内部类的实例方法也不能访问外部类的实例成员，只能访问外部类的静态成员。


静态内部类是外部类的一个静态成员，因此外部类的静态方法、静态初始化也可以使用静态内部类来定义变量、创建对象等。

<font>外部类依然不能直接访问静态内部类的成员，但可以使用静态内部类的类名作为调用者来访问静态内部类的类成员，也可以使用静态内部类对象作为调用者来访问静态内部类的实例成员。</font>


**使用示例:**
```
class StaticInnerClassTest {
    // 外部类实例成员
    private int propl=5;
    // 外部类静态成员
    private static int prop2=9;
    
    void display() { 
        StaticInnerClass in=new StaticInnerClass();
        in.display();
    }
    // 静态内部类
    static class StaticInnerClass {
        //内部类里的静态成员
        private static int age;
        
        public void display() {
            //下面代码出现错误-静态内部类无法访问外部类的实例成员
            // System.out.println(propl);
            
            //下面代码正常
            System.out.println(prop2);
        }
    }
}

public class ObjectStaticDemo {
    public static void main(String[]args) {
        StaticInnerClassTest outer=new StaticInnerClassTest();
        outer.display(); // 输出:9
    }
}
```

## 3.4 匿名内部类
匿名内部类由于没有名字，所以它的创建方式也比较特别。创建格式如下。
```
new 父类构造器（参数列表）|实现接口（）{
    //匿名内部类的类体部分
}
```
> 匿名内部类我们必须要继承一个父类或者实现一个接口。同时它没有class关键字，这是因为匿名内部类是直接使用new来生成一个对象的引用。当然这个引用是隐式的。

**使用示例**
```java
abstract class Bird {
    private String name;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name=name;
    }
    public abstract int fly();
}

public class AnonymousInnerClass {

    public void test(Bird bird) {
        System.out.println(bird.getName()+"最高能飞"+bird.fly()+"米");
    }
    
    public static void main(String[]args) {
        // 声明一个名为 animal 的AnonymousInnerClass对象
        AnonymousInnerClass animal=new AnonymousInnerClass();
        
        // 调用实例方法,并传入一个匿名对象
        animal.test( new Bird() {
            public int fly() {
                return 1000;
            }
            public String getName() {
                return"小鸟";
            }
        }
    }
}
```

**代码详解**

`在AnonymousInnerClass类中，test()方法接受一个Bird类型的参数，同时我们知道一个抽象类是没有办法直接new的，我们必须要先有实现类才能new出来它的实现类实例。所以在main方法中直接使用匿名内部类来创建一个Bird实例。
匿名内部类不能是抽象类，所以必须要实现它的抽象父类或者接口里面所有的抽象方法。`


> 匿名内部类存在一个缺陷，就是它仅能被使用一次，创建匿名内部类时它会立即创建一个该类的实例，该类的定义会立即消失，所以匿名内部类不能够被重复使用。


# 4. 匿名对象
匿名对象，顾名思义就是没有明确的声明的对象。读者也可以简单地理解为只使用一次的对象，即没有任何一个具体的对象名称引用它

**使用示例:**
```
class Person{
	private String name="张三";
	private int age=25;
	public String talk(){
		return"我是："+name+"，今年："+age+"岁";
	}
}
public class AnonymousObject {
	public static void main(String[]args) {
	  // 使用匿名对象
	  System.out.println(new Person().talk());
	}
}
```


