---
title: 六、Go接口
date: 2018-04-14 18:54:35
tags:
 - go
categories:
 - 后端
---

> Go 语言不是一种 “传统” 的面向对象编程语言：它里面没有类和继承的概念。但是 Go 语言里有非常灵活的`接口`概念，通过它可以实现很多面向对象的特性

# 1. 什么是interface(接口)
`简单地说，interface是一组method的组合，但是这些method不包含（实现）代码,我们通过interface来定义对象的一组行为。`
> 接口里不能包含变量

# 2. 接口声明
```
type Namer interface {
    Method1(参数列表1) 返回值列表1
    Method2(参数列表2) 返回值列表
    ...
}
```
示例

```
// 变量名未忽略
type writer interface{
    Write(p []byte) (n int, err error)
}

// 变量名被忽略
type writer interface{
     Write([]byte) (int, error)
}
```

# 3. 接口规范
- 接口类型名：方法的接口名,<font color=red>由方法名加 [e]r 后缀组成</font>，例如 Printer、Reader、Writer、Logger、Converter 等等。还有一些不常用的方式（当后缀 er 不合适时），比如 Recoverable，此时接口名以 able 结尾，或者以 I 开头。

- 方法名：当方法名首字母是大写时，且这个接口类型名首字母也是大写时，这个方法可以被接口所在的包（package）之外的代码访问。
- 参数列表、返回值列表：参数列表和返回值列表中的参数变量名可以被忽略，
- Go 语言中的接口都很简短，通常它们会包含 0 个、最多 3 个方法
- 接口里的所有方法都没有方法体，即接口的方法都是没有实现的方法。接口体现了程序设计的多态和高内聚低偶合的思想


# 4. 接口嵌套(接口继承)
> 在Go语言中，不仅结构体与结构体之间可以嵌套，接口与接口间也可以通过嵌套创造出新的接口。
接口与接口嵌套组合而成了新接口，只要接口的所有方法被实现，则这个接口中的所有嵌套接口的方法均可以被调用

`比如接口 File 包含了 ReadWrite 和 Lock 的所有方法，它还额外有一个 Close() 方法`

```
type ReadWrite interface {
    Read(b Buffer) bool
    Write(b Buffer) bool
}

type Lock interface {
    Lock()
    Unlock()
}

type File interface {
    ReadWrite
    Lock
    Close()
}
```
> <font color=red>一个接口(比如 A 接口)可以继承多个别的接口(比如 B,C 接口)，这时如果要实现 A 接口，也必须将 B,C 接口的方法也全部实现


# 5. 接口实现
如果一个类型实现了一个接口里的所有方法，那么这个类型就实现了这个接口

如下面示例，类型fileHandle 实现了DataWrite接口:
```
type DataWrite interface {
	Write(data interface{}) error
}

type fileHandle struct {
      ....
}

func (f fileHandle)Write(data interface{}) error  {
	fmt.Println("文件写入中....")
	return nil
}

```
# 6.类型与接口的关系
类型和接口之间有一对多和多对一的关系。

## 6.1 一对多
一个类型可以同时实现多个接口，而接口间彼此独立，不知道对方的实现。
```
// 定义Writer接口
type Writer interface{
	Write([]byte) int
}
// 定义Reader接口
type Reader interface{
	Read()
}

// 定义一个File类型
type File struct {

}

// File类型 实现Writer接口
func (f File)Write(p []byte) int {
	return 0
}

// File类型 定义Reader接口
func (f File)Read() {

}
```
## 6.2 多对一
一个接口的方法，不一定需要由一个类型完全实现，接口的方法可以通过在类型中嵌入其他类型或者结构体来实现。也就是说，使用者并不关心某个接口的方法是通过一个类型完全实现的，还是通过多个结构嵌入到一个结构体中拼凑起来共同实现的。

- 示例分析

Service接口定义了两个方法：一个是开启服务的方法:Start()，一个是输出日志的方法:Log()。使用GameService结构体来实现Service，GameService结构只能实现Start()方法，Service接口中的Log()方法被日志器（Logger）实现了，将Logger嵌入到GameService中，则达成了GameService结构体实现Service接口，从而能最大程度地避免代码冗余，简化代码结构。

详细实现过程如下：
```
01  // 一个服务需要满足能够开启和写日志的功能
02   type Service interface {
03       Start()          // 开启服务
04       Log(string)     // 日志输出
05   }
06
07  // 日志器
08   type Logger struct {
09   }
10
11  // 实现Service的Log()方法
12   func (g ＊Logger) Log(l string) {
13
14   }
15
16  // 游戏服务
17   type GameService struct {
18       Logger                                    // 嵌入日志器
19   }
20
21  // 实现Service的Start()方法
22   func (g ＊GameService) Start() {
23   }
```
代码说明如下：
- 第2行，定义服务接口，一个服务需要实现Start()方法和日志方法。
- 第8行，定义能输出日志的日志器结构。
- 第12行，为Logger添加Log()方法，同时实现Service的Log()方法。
- 第17行，定义GameService结构
- 第18行，在Game Service中嵌入Logger日志器，以实现日志功能。
- 第22行，Game Service的Start()方法实现了Service的Start()方法。

此时，实例化GameService，并将实例赋给Service，代码如下：
```
var s Service = new(Game Service)
s.Start()
s.Log("hello")
```
s就可以使用Start()方法和Log()方法，其中，Start()由GameService实现，Log()方法由Logger实现。


# 7. 接口和类型间转换 
Go语言中使用接口断言（type assertions）将接口转换成另外一个接口，也可以将接口转换为另外的类型。接口的转换在开发中非常常见，使用也非常频繁。


## 7.1 类型断言的格式
如果发生接口未实现时，将会把ok置为false，t置为T类型的0值。正常实现时，ok为true。
这里ok可以被认为是：`i接口是否实现T类型的结果`。

类型断言的基本格式如下：
```
t,ok := i.(T)
```
- i 代表接口变量。
- T 代表转换的目标类型。
- t 代表转换后的变量。

## 7.2 将接口转为其他接口
`实现某个接口的类型同时实现了另外一个接口，此时可以在两个接口间转换。`

- 代码示例

鸟和猪具有不同的特性，鸟可以飞，猪不能飞，但两种动物都可以行走。如果使用结构体实现鸟和猪，让它们具备自己特性的Fly()和Walk()方法就让鸟和猪各自实现了飞行动物接口（Flyer）和行走动物接口（Walker）。
将鸟和猪的实例创建后，被保存到interface{}类型的map中。interface{}类型表示空接口，意思就是这种接口可以保存为任意类型。对保存有鸟或猪的实例的interface{}变量进行断言操作，如果断言对象是断言指定的类型，则返回转换为断言对象类型的接口；如果不是指定的断言类型时，断言的第二个参数将返回false。

实现代码如下：
```
01   package main
02
03   import "fmt"
04
05  // 定义飞行动物接口
06   type Flyer interface {
07        Fly()
08   }
09
10  // 定义行走动物接口
11   type Walker interface {
12        Walk()
13   }
14
15  // 定义鸟类
16   type bird struct {
17   }
18
19  // 实现飞行动物接口
20   func (b ＊bird) Fly() {
21        fmt.Println("bird: fly")
22   }
23
24  // 为鸟添加Walk()方法，实现行走动物接口
25   func (b ＊bird) Walk() {
26        fmt.Println("bird: walk")
27   }
28
29  // 定义猪
30   type pig struct {
31   }
32
33  // 为猪添加Walk()方法，实现行走动物接口
34   func (p ＊pig) Walk() {
35        fmt.Println("pig: walk")
36   }
37
38   func main() {
39
40       // 创建动物的名字到实例的映射
41        animals := map[string]interface{}{
42              "bird": new(bird),
43              "pig":  new(pig),
44        }
45
46       // 遍历映射
47         for name, obj := range animals {
48
49           // 判断对象是否为飞行动物
50              f, isFlyer := obj.(Flyer)
51           // 判断对象是否为行走动物
52              w, isWalker := obj.(Walker)
53
54             fmt.Printf("name: %s is Flyer: %v is Walker: %v\n", name, is Flyer, is Walker)
55
56           // 如果是飞行动物则调用飞行动物接口
57              if isFlyer {
58                    f.Fly()
59             }
60
61            // 如果是行走动物则调用行走动物接口
62              if isWalker {
63                    w.Walk()
64             }
65        }
66   }
```

代码说明如下：
- 第6行定义了飞行动物的接口。
- 第11行定义了行走动物的接口。
- 第16和30行分别定义了鸟和猪两个对象，并分别实现了飞行动物和行走动物接口。
- 第41行是一个map，映射对象名字和对象实例，实例是鸟和猪。
- 第47行开始遍历map，obj为interface{}接口类型。
- 第50行中，使用类型断言获得f，类型为Flyer及isFlyer的断言成功的判定。
- 第52行中，使用类型断言获得w，类型为Walker及isWalker的断言成功的判定。
- 第57和62行，根据飞行动物和行走动物两者

代码输出如下：
```
name: pig is Flyer: false is Walker: true
pig: walk
name: bird is Flyer: true is Walker: true
bird: fly
bird: walk
```


# 8.空接口 — 能保存所有值的类型
空接口是接口类型的特殊形式，`空接口没有任何方法`，因此任何类型都无须实现空接口。从实现的角度看，任何值都满足这个接口的需求。因此空接口类型可以保存任何值，也可以从空接口中取出原值。


## 8.1 将值保存到空接口
```
01   var any interface{}
02
03   any = 1
04   fmt.Println(any)
05
06   any = "hello"
07   fmt.Println(any)
08
09   any = false
10   fmt.Println(any)
```
代码输出如下：
```
1
hello
false
```

- 第1行，声明any为interface{}类型的变量。
- 第3行，为any赋值一个整型1。
- 第4行，打印any的值，提供给fmt.Println的类型依然是interface{}。
- 第6行，为any赋值一个字符串hello。此时any内部保存了一个字符串。但类型依然是interface{}。
- 第9行，赋值布尔值。

## 8.2 从空接口获取值
保存到空接口的值，如果直接取出指定类型的值时，会发生编译错误

代码如下：
```
01  // 声明a变量，类型int，初始值为1
02   var a int = 1
03
04  // 声明i变量，类型为interface{}，初始值为a，此时i的值变为1
05   var i interface{} = a
06
07  // 声明b变量，尝试赋值i
08  var b int = i
```
第8行代码编译报错：
```
cannot use i (type interface {}) as type int in assignment: need type assertion
```
> 编译器告诉我们，不能将i变量视为int类型赋值给b。在代码第5行中，将a的值赋值给i时，虽然i在赋值完成后的内部值为int，但i还是一个interface{}类型的变量。

为了让第8行的操作能够完成，`编译器提示我们得使用type assertion，意思就是类型断言`。


使用类型断言修改第8行代码如下：
```
var b int = i.(int)
```
修改后，代码可以编译通过，并且b可以获得i变量保存的a变量的值：1。

## 8.3 空接口的值比较
空接口在保存不同的值后，可以和其他变量值一样使用“==”进行比较操作。空接口的比较有以下几种特性。

### 8.3.1 类型不同的空接口间的比较结果不相同

保存有类型不同的值的空接口进行比较时，Go语言会优先比较值的类型。因此类型不同，比较结果也是不相同的，代码如下：
```
01  // a保存整型
02   var a interface{} = 100
03
04  // b保存字符串
05   var b interface{} = "hi"
06
07  // 两个空接口不相等
08   fmt.Println(a == b) // 输出: false
```

### 8.3.2 不能比较空接口中的动态值
当接口中保存有动态类型的值时，运行时将触发错误，

代码如下：
```
01  // c保存包含10的整型切片
02   var c interface{} = []int{10}
03
04  // d保存包含20的整型切片
05   var d interface{} = []int{20}
06
07  // 这里会发生崩溃
08   fmt.Println(c == d)
```
代码运行到第8行时发生崩溃：
```
panic: runtime error: comparing uncomparable type []int
```
这是一个运行时错误，提示[]int是不可比较的类型。
zhuoge
- 下面列出了几种类型及比较情况

类型 | 说明
---|---
map | 宕机错误，不可比较
切片（[]T）| 宕机错误，不可比较
通道（channel）| 可比较，必须由一个make生成，也就是说同一个通道才会true，否则false
数组([容量]T) | 可比较，编译期知道两个数组是否一致
结构体|可比较，可以逐个比较结构体的值


# 9.接口使用中的注意事项
- 接口本身不能创建实例,但是可以指向一个实现了该接口的自定义类型的变量(实例)

```
package main

import "fmt"

type PeopleI interface {
	GetName() string
}

type Girl struct {
	Name string
}

func (g Girl) GetName() string {
	return g.Name
}
func main() {
	var p PeopleI
	var g Girl
	g.Name = "小花"
	p = g //指向一个实现了该接口的自定义类型的变量
	fmt.Println(p.GetName())

}

```
- 接口中所有的方法都没有方法体,即都是没有实现的方法。
- 在 Go中，一个自定义类型需要将某个接口的所有方法都实现，我们说这个自定义类型实现 了该接口。
- 一个自定义类型只有实现了某个接口，才能将该自定义类型的实例(变量)赋给接口类型
-  只要是自定义数据类型，就可以实现接口，不仅仅是结构体类型
-  一个自定义类型可以实现多个接口
-  Go接口中不能有任何变量
-   interface类型默认是一个指针(引用类型)，如果没有对interface初始化就使用，那么会输出nil
-   空接口 interface{} 没有任何方法，所以所有类型都实现了空接口, 即我们可以把任何一个变量 赋给空接口


