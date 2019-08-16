---
title:  Go数据类型
date: 2018-04-04 11:25:29
tags:
 - go
categories:
 - 后端
---

 ## 1. 数据类型
> Go语言将数据类型分为四类:基础类型、复合类型、引用类型和接口类型;


### 1.1 基础数据类型
![](https://mrliuqh.github.io/directionsImg/go/dataType1.png)

### 1.2 复合数据类型
![](https://mrliuqh.github.io/directionsImg/go/dataType2.png)


<!--more-->
#### 1.2.1 数组

```
语法: var 数组变量名 [数组长度]T

说明：
    ● 数组变量名：数组声明及使用时的变量名。
    ● 元素数量：数组的元素数量。
    可以是一个表达式，但最终通过编译期计算的结果必须是整型数值。
    也就是说，元素数量不能含有到运行时才能确认大小的数值。
    ● T可以是任意基本类型，包括T为数组本身。但类型为数组本身时，可以实现多维数组

示例：
var arr [3]int  //默认初始化0
var q [3]int = [3]int{1,2,3}
q := [...]int{1,2,3}
q := [...]int{90:-1}//key和value的赋值方式，下表90的值为-1，数组长度为91

```
> 如果在`数组的长度`位置出现的是“...”省略号，则表示数组的长度是根据初始化值的 个数来计算


#### 1.2.2 切片
 ##### 创建slice主要两种：1.基于数组创建。2.直接创建
 - 1.基础数组创建
```
  arrVar := [4]int{1, 2, 3，4} 
  
  sliceVar := arrVar[1:3]
```
> 数组arrVar和sliceVar里面的地址其实是一样的，也就是说如果你改变sliceVar里面的变量，那么arrVar里面的变量也会随之改变。

- 2.直接创建
 ```
myslice1 := make([]int,5)//创建一个元素个数5的slice,cap也是5
myslice2 := make([]int,5,10)//创建一个元素个数5的slice，cap是10
myslice3 := []int{1,2,3,4}//创建一个元素个数为4的slice，cap是4
var slice []int //创建一个空的slice，cap和len都是0
```
> 可以使用内置的make()函数来创建。事实上还是会创建一个匿名的数组，只是不需要我们来定义。

#### 1.2.3 map
 ##### 可以使用内建函数 make 也可以使用 map 关键字来定义 Map:
 ```
 /* 声明变量，默认 map 是 nil */
var myMap map[keyType]valueType

//示例1
ages := map[string] int {
    "tom": 21,
    "anny": 18,
}

/* 使用 make 函数 */
myMap := make(map[keyType]valueType)

//示例1相当于
ages := make(map[string]int)
ages["tom"] = 21
ages["anny"] = 18

 ```
> myMap是声明的变量名，keyType是对应的Key的类型，valueType是value的类型。
 
 
 #### 1.2.4 结构体
> 结构体是一种聚合的数据类型，是由零个或多个任意类型的值聚合成的实体。每个值称为结构体的成员。

```
 /* 声明结构体 */
type 结构体名称 struct {
    属性1    类型
    属性2    类型
    ...
}

/* 使用示例 */
type User struct {
    name    string
    age     int
}

user := new(User)
user.name = "tom"
user.age = 20

```
> 结构体的初始化可以使用new关键词和var关键词，不同的是如果使用new，则返回类型是一个指针，使用var，则是结构体自身。