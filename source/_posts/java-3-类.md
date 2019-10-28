---
title: 学Java笔记-类
date: 2019-10-15 19:19:48
tags:
 - Java
categories:
 - 后端
---
# 1.类
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


