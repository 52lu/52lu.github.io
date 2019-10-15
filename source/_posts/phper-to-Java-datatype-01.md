---
title: 学Java笔记-原始数据类型及运算
date: 2019-10-12 17:45:29
tags: 
 - Java
categories:
 - 后端
---

# 1.变量和常量
## 1.1 关键字
**关键字不能作为变量名使用,以下表格中是java中的关键字列表**

![](https://mrliuqh.github.io/directionsImg/java/keyword.jpg)

> true、false、null不是关键字，是保留字，但是仍然不能用于命名标识符。保留字是为Java预留的关键字，它们虽然现在没有作为关键字，但在以后的升级版本中有可能作为关键字。


## 1.2 变量
### 1.2.1 变量声明
#### 单一变量声明

**① 先声明后赋值:**
```
变量类型 变量名
```
*实例*
```
String studentName;
studentName = "小明";
```

**② 声明及赋值:**
```
String studentName = "小明";
```

#### 多个变量声明
```
// 只声明
String studentName, className;

// 声明及赋值
String studentName = "小明", className ="三年级";
```


## 1.3 常量
常量分为字面常量和字符常量两种。
- 字面常量：指的是如123、12.34、‘m’、true这样字面上本身就是一个固定值的数据。
- 字符常量：指的是代表一个固定值的标识符。使用字符常量需要在程序中先声明后使用。

### 1.3.1 常量声明
声明字符常量的语法如下。
```
final 常量类型  常量标识符=常量值；
```

**① 声明及赋值:**

```
//声明一个float类型常量，并初始化为3.14
final float PI = 3.14F; 

//声明一个整型常量，并初始化为24
final int STUENT_NUMBER = 24;  
```

**② 先声明后赋值:**
```
final float PI;                     //声明一个float类型常量
final int STUENT_NUMBER;           //声明一个整型常量
PI = 3.14F;                         //初始化为3.14
STUENT_NUMBER = 24;                 //初始化为24
```
> 初始化以后，就不允许再在程序中对这些常量进行重新赋值，即不允许改变常量的值。

### 1.3.2 批量声明
如果需要声明多个同一类型的常量，可以使用下面的语法：
```
final常量类型  常量标识符1,常量标识符2,常量标识符3，…;

final常量类型  常量标识符1=常量值1,常量标识符2=常量值2,常量标识符3=常量值3;
```
**实例**
```
 //声明三个float类型的变量
final float PI，PRICE，WEIGHT;  

//声明三个float类型的变量，同时进行初始化
final float PI=3.14F,price=13.86F,WEIGHT=86.32F;
```

       
                                        
## 1.4 命名规则
- 一个标识符可以由几个单词连接而成，以表明它所代表的含义，如applePrice。

-  如果是类名，每个单词的首字母都要大写，其他字母则小写，如StudentInfo。
- 如果是方法名或者变量名，第一个单词的首字母小写，其他单词的首字母都要大写，如getStudentInfo()、studentName。
-  如果是常量，所有单词的所有字母全部大写，如果由多个单词组成，通常情况下单词之间用下画线“_”分隔，如PI、MAX_VALUE。
- 如果是包名，所有单词的所有字母全部小写，如examples.chapter02。



# 2.数据类型
![](https://mrliuqh.github.io/directionsImg/java/basic_data_type.jpg)

## 2.1 整数类型
<b>整数类型取值范围</b>
![](https://mrliuqh.github.io/directionsImg/java/int_ranage.jpg)


```
byte age = 18;
short number = 2300;
long account = 8172651756L;
```
> 在为long型常量或变量赋值时，需要在所赋值的后面加上一个字母"L"（或小写"l"），说明所赋的值为long型。如果所赋的值未超出int型的取值范围，也可以省略字母"L"（或小写"l")

```
//所赋的值超出了int型的取值范围，必须在后面加上字母“L”
long account = 3124567893L;

//所赋的值未超出int型的取值范围，可以在后面加上字母“L”
long account = 31245678L; 

//所赋的值未超出int型的取值范围，可以省略字母“L”
long account = 31245678;  


// !但是下面的这种赋值方式是错误的。
long account = 3124567893; //所赋的值超出了int型的取值范围，但是没有在后面加上字母“L”
```

## 2.2 浮点类型
<b>浮点类型取值范围</b>
![](https://mrliuqh.github.io/directionsImg/java/float_ranage.jpg)

如果要声明一个用来保存小数的变量，必须使用浮点类型;
```
//声明一个float类型的变量，并赋值
float price = 326.85F;

//声明一个double类型的变量，并赋值
double height = 3836.8;
double height = 3836.8D;
```
> 在默认情况下，小数都被看作double型。若使用float型小数，则需要在其值后面添加F或f,否则编译时，会提示错误。因为不加上字母“F”或小写“f”时，默认的数值为double类型，把double类型的数值赋给float类型的变量，是不正确的。而在为double类型变量赋值时，后面的字母“D”或者“d”,可加可不加。

## 2.3 字符类型
字符类型（char）用于存储单个字符，占用16位（两个字节）的内存空间,在定义字符型变量时，要以单引号表示,如'a'、'#'、'3'。而双引号则表示一个字符串;

```
//声明一个char类型的变量，用来存储性别信息
char gender = '男';

//声明一个char类型的变量，用来存储符号'*'
char star = '*';

//声明一个char类型的常量，用来存储圆周率符号'π'
final char PI = 'π';       
```
## 2.4 逻辑类型（布尔类型）
声明逻辑类型常量或者变量，需要使用关键字 boolean,
```
boolean falg = false;
```

# 3.数据类型转换

**数据类型级别与取值范围**
![](https://mrliuqh.github.io/directionsImg/java/data_type_level.jpg)

## 3.1 自动（隐式）类型转换
在对变量进行赋值时，如果是将低级数据类型（相对取值范围较小的数据类型）的数据，赋给高级数据类型（相对取值范围较大的数据类型）的变量，这时Java会自动完成从低级类型向高级类型的转换。在原始数据类型中，除了boolean类型外，其他数据均可参与算术运算。

**实例**
```
//定义float类型的变量，并赋值
float price = 36; 

//输出price变量的值：price=36.0
System.out.println("price="+price);        

```
`可以看出，因为相对于float类型来说，整数36为int型，属于低级数据类型，所以Java自动将其转换为float类型的36.0`
> 将低类型的数据赋给高类型的变量时，Java会自动将低类型的数据转换为高类型的数据，然后赋值。

## 3.2 混合数据类型运算
在不同类型的数据进行混合运算时，分为两种情况：
- 只有byte、short或char类型的数据。
这种情况下，Java首先将byte或short或char类型转换为int类型，然后再参与运算，并且运算结果也是int型的

*例如：*
```
public class Test3{
    public static void main(String[] args){
        byte      byteVar = 68;             //声明一个byte类型的字节变量
        short  shortVar = 873;              //声明一个short类型的整型变量
        char  charVar = ‘a’;               //声明一个char类型的字符变量
        int  value = byteVar + shortVar + charVar;
        System.out.println(“value = ” + value);//输出value的值：value=1038
    }
}
```
- 含有int、long、float或double型的数据。
这种情况下，Java首先将所有低数据类型转换为表达式中数据类型最高的数据类型，然后再参与运算，并且运算结果也是表达式中最高的数据类型。

*例如：*
```
public class Test4{
    public static void main(String[] args){
        //声明一个字节类型的变量
        byte  byteVar = 68;
        //声明一个int类型的变量
        int   intVar = 873; 
        //声明一个float类型的变量
        float  floatVar = 128.5F; 
        // 不同类型进行混合运算
        double  value = byteVar + intVar + floatVar;
        System.out.println("value = " + value) // 输出:1069.5
    }
}
```

## 3.2 强制（显式）类型转换
在对变量进行赋值时，如果是将高级数据类型的数据赋值给低级数据类型的变量，java 不会自动完成类型的转换，需要主动转换；

```
// 定义int类型的变量，并赋值double类型的36.5
int count = 36.5;
System.out.println("count="+count);
```
上述代码在编译时会提示错误，因为将高级类型的数据36.5赋给了低级类型的变量count。

可改为下面这种形式：
```
//强制类型转换
int count = (int)36.5;  

//输出count变量的值：count=36         
System.out.println("count="+count);
```

> 在上面的代码中，（int）显示为强制类型转换的语法，即在欲转换的数据或变量前面，用“（目的数据类型）”的形式，强制转换为括号中的目的数据类型。另外，在上面的示例中也可以看到，<font color=red>在进行强制类型转换时，进行了简单的截断而不是四舍五入（输出的是36，而不是37）。</font>


# 4.运算符
## 4.1 算术运算符
![](https://mrliuqh.github.io/directionsImg/java/arithmetic_operation.jpg)

## 4.2 赋值运算符
![](https://mrliuqh.github.io/directionsImg/java/assignment_operation.jpg)

## 4.3 关系运算符
![](https://mrliuqh.github.io/directionsImg/java/relational_operation.jpg)

## 4.4 位运算符
![](https://mrliuqh.github.io/directionsImg/java/bit_operation.png)

## 4.5 逻辑运算符
![](https://mrliuqh.github.io/directionsImg/java/logic_operation.png)

## 4.6 三元运算符
```
逻辑表达式 ？ 表达式1（为true） : 表达式2 (为false) 
```

## 4.7 运算符优先级
![](https://mrliuqh.github.io/directionsImg/java/operator_precedence.jpg)

