---
title: Go-基础篇-变量及数据类型
date: 2018-04-03 15:46:01
tags:
 - go
categories:
 - 后端编程
---

## 1.变量声明
### 1.1 使用var关键字
> var 变量名 变量类型  = [变量值]

```
//定义多个变量
var vname1,vname2,vname3 type

//定义变量,并初始化
var vname1 int = 1

//同时初始化多个变量
var vname1,vname2,vname3 = v1,v2,v3 
```

### 1.2 使用 := 

```
//定义变量,并初始化
vname1 := 1

//同时初始化多个变量
vname1,vname2,vname3 := v1,v2,v3 
// 示例: var a, b, c = 3, 4, "foo"

```
### 1.3 分组声明
在Go语言中，同时声明多个常量、变量，或者导入多个包时，可采用分组的方式进行声明

```
// 同时导入多个包
import (
	"fmt"
	"errors"
)

// 声明多个常量
const (
  i = 100,
  pi = 3.14
  prex = "Go_"
)

// 声明多个变量
var (
  a int,
  b string = "hello",
  
)
```

### 1.4 特殊变量: _(下划线)

`任何赋予它的值都会被丢弃,Go语言对于已声明但未使用的变量会在编译阶段报错,可以通过其来丢弃`

```
_, b := 1,2
```

### 1.5 make和new:

1. new(T) 返回的是T的指针
`new(T) 为一个 T 类型新值分配空间并将此空间初始化为 T 的零值，返回的是新值的地址，也就是T类型的指针 *T，该指针指向T的新分配的零值。`
2. make 只能用于 slice，map，channel 三种类型:
`make(T, args) 返回的是初始化之后的 T 类型的值，这个新值并不是 T 类型的零值，也不是指针 *T，是经过初始化之后的 T 的引用。`

## 2.基础类型

### 2.1 整数
整数类型有无符号和带符号两种。Go语言同时支持int和uint，这两种类型的长度相同，但具体长度取决于不同编译器的实现。当前的gcc和gccgo编译器在32位和64位平台上都使用32位来表示int和uint，但未来在64位平台上可能增加到64位。Go语言里面也有直接定义好位数的类型：rune，int8，int16，int32，int64和byte，uint8，uint16，uint32，uint64。`其中rune是int32的别称，byte是uint8的别称`


### 2.2 浮点数
浮点类型有float32 和 浮点类型有float64 两种，默认是float64

### 2.3 复数
默认类型是complex128（64位实数＋64位虚数）。如果需要小一些的，也有complex64（32位实数＋32位虚数）。复数的形式为RE＋IMi，其中RE是实数部分，IM是虚数部分，而最后的i是虚数单位
```
var c complex64 = 5 + 5i
```

### 2.4 布尔类型
布尔值的类型为bool，值是true或false，默认为false

### 2.5 字符串
字符串都是采用UTF-8字符集编码。字符串是用一对双引号（""）或反引号（` `）括起来定义,`字符串不可变，可以用 + 操作符 连接两个字符串` 

```
// 单行字符串
s := "hello word"

// 多行字符串

s := `这是一个段落，
此处的换行会原样输出。
`
```



### 2.6 错误类型
Go语言内置有一个error类型，专门用来处理错误信息，Go语言的package里面还专门有一个包errors来处理错误
```
 err := errors.New("error:我自己定义的错误")
 if err != nil {
     fmt.Print(err)
 }
```

### 2.7 常量
程序编译阶段就确定下来的值，而程序在运行时则无法改变该值。在Go语言程序中，常量可定义为数值、布尔值或字符串等类型。

语法:
> const 常量名 [常量类型] = 常量值
 
```
const PI float32 = 3.1415
const a = 100
const Prex = "Go_"
```

- iota 枚举

Go语言里面有一个关键字iota，这个关键字用来声明enum的时候采用，它默认开始值是0，每调用一次加1。，每个const分组的第一个常量被默认设置为它的0值，第二及后续的常量被默认设置为它前面那个常量的值，如果前面那个常量的值是iota，则它也被设置为iota。

```
const(
    x = iota  // x == 0
    y = iota  // y == 1
    z = iota  // z == 2
    w  // 常量声明省略值时，默认和之前一个值的字面相同。这里隐式地说 w = iota，因此w == 3。其实上面y和z可同样不用"= iota"
)
const v = iota // 每遇到一个const关键字，iota就会重置，此时v == 0
```        
> 每遇到一个const关键字，iota就会重置

 
## 3.复合类型


### 3.1 数组

```
语法: var 数组变量名 [数组长度]T

说明：
 1.数组变量名：数组声明及使用时的变量名。
 2.元素数量：数组的元素数量。可以是一个表达式，但最终通过编译期计算的结果必须是整型数值。也就是说，元素数量不能含有到运行时才能确认大小的数值。
 3. T可以是任意基本类型，包括T为数组本身。但类型为数组本身时，可以实现多维数组

示例：
var arr [3]int  //默认初始化0
var q [3]int = [3]int{1,2,3}
q := [...]int{1,2,3}
q := [...]int{90:-1}//key和value的赋值方式，下表90的值为-1，数组长度为91

```
> 如果在 `数组的长度` 位置出现的是“...”省略号，则表示数组的长度是根据初始化值的 个数来计算


### 3.2 切片
  
创建slice主要两种：1.基于数组创建。2.直接创建
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


### 3.3 map
可以使用内建函数 make 也可以使用 map 关键字来定义 Map:

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
 
 
### 3.3 结构体
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
`字段直接没有逗号,`

> 结构体的初始化可以使用new关键词和var关键词，不同的是如果使用new，则返回类型是一个指针，使用var，则是结构体自身。