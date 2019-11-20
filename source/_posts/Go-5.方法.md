---
title: 学Go笔记-方法
date: 2018-04-11 14:47:17
tags:
 - go 
categories:
 - 后端编程
---

> Golang 中的方法是作用在指定的数据类型上的(即:和指定的数据类型绑定)，因此自定义类型，都可以有方法，`而不仅仅是 struct`

# 1.方法的声明

Go语言中的方法（Method）是一种作用于特定类型变量的函数。这种特定类型变量叫做接收器（Receiver）。
如果将特定类型理解为结构体或“类”时，接收器的概念就类似于其他语言中的this或者self。
在Go语言中，接收器的类型可以是任何类型，不仅仅是结构体，任何类型都可以拥有方法。

## 1.1 声明格式

```
func (接收器变量 接收器类型) 方法名(参数列表) (返回参数) {
     函数体
}
```

- 接收器变量：接收器中的参数变量名在命名时，官方建议使用接收器类型名的第一个小写字母，而不是self、this之类的命名。例如，Socket类型的接收器变量应该命名为s，Connector类型的接收器变量应该命名为c等。
- 接收器类型：接收器类型和参数类似，可以是指针类型和非指针类型。
- 方法名、参数列表、返回参数：格式与函数定义一致。


## 1.2 声明示例
```
type people struct {
	Name, Like , Sex   string
	Height, Weight float32
}

func (p people) walk() {
	fmt.Println(p.Name + " 在行走...")
}

func RunStruct()  {
	var xiaomi people
	xiaomi.Name = "小明"
	xiaomi.Name = "小明"
	p := people{"小明","跑步", "男", 170.0, 62.6}
	p.walk()
}

func main() {
	RunStruct()
	//out: 小明 在行走...
}
```

# 2.接收器分类
## 2.1 指针类型的接收器
理解指针类型的接收器
指针类型的接收器由一个结构体的指针组成，由于指针的特性，调用方法时，修改接收器指针的任意成员变量，在方法结束后，修改都是有效的。

在下面的例子，使用结构体定义一个属性（Property），为属性添加SetValue()方法以封装设置属性的过程，通过属性的Value()方法可以重新获得属性的数值。使用属性时，通过SetValue()方法的调用，可以达成修改属性值的效果。

```
01   package main
02
03   import "fmt"
04
05  // 定义属性结构
06   type Property struct {
07       value int  // 属性值
08   }
09
10  // 设置属性值
11   func (p ＊Property) SetValue(v int) {
12
13       // 修改p的成员变量
14        p.value = v
15   }
16
17  // 取属性值
18   func (p ＊Property) GetValue() int {
19        return p.value
20   }
21
22   func main() {
23
24       // 实例化属性
25        p := new(Property)
26
27       // 设置值
28        p.SetValue(100)
29
30       // 打印值
31        fmt.Println(p.GetValue())
32       // out: 100
33   }
```
代码说明如下：
- 第6行，定义一个属性结构，拥有一个整型的成员变量。
- 第11行，定义属性值的方法。
- 第14行，设置属性值方法的接收器类型为指针。因此可以修改成员值，即便退出方法，也有效。
- 第18行，定义获取值的方法。
- 第25行，实例化属性结构。
- 第28行，设置值。此时成员变量变为100。
- 第31行，获取成员变量。



## 2.2 非指针类型的接收器
当方法作用于非指针接收器时，Go语言会在代码运行时将接收器的值复制一份。在非指针接收器的方法中可以获取接收器的成员值，但修改后无效。
点（Point）使用结构体描述时，为点添加Add()方法，这个方法不能修改Point的成员X、Y变量，而是在计算后返回新的Point对象。Point属于小内存对象，在函数返回值的复制过程中可以极大地提高代码运行效率，详细过程请参考下面的代码。
```
01   package main
02
03   import (
04        "fmt"
05   )
06
07  // 定义点结构
08   type Point struct {
09        X int
10        Y int
11   }
12
13  // 非指针接收器的加方法
14   func (p Point) Add(other Point) Point {
15
16       // 成员值与参数相加后返回新的结构
17        return Point{p.X + other.X, p.Y + other.Y}
18   }
19
20   func main() {
21
22       // 初始化点
23        p1 := Point{1, 1}
24        p2 := Point{2, 2}
25
26       // 与另外一个点相加
27        result := p1.Add(p2)
28
29       // 输出结果
30        fmt.Println(result)
31       // out: {3 3}
32   }
```
代码说明如下：
- 第8行，定义一个点结构，拥有X和Y两个整型分量。
- 第14行，为Point结构定义一个Add()方法。
- 第23和24行，初始化两个点p1和p2。
- 第27行，将p1和p2相加后返回结果。
- 第30行，打印结果。

`由于例子中使用了非指针接收器，Add()方法变得类似于只读的方法，Add()方法内部不会对成员进行任何修改。`

## 2.3 指针和非指针接收器的使用
- 小对象由于值复制时的速度较快，适合使用非指针接收器。
- 大对象因为复制性能较低，适合使用指针接收器，在接收器和参数间传递时不进行复制，只是传递指针。


# 3. 嵌入结构体扩展类型
结构体允许其成员字段在声明时没有字段名而只有类型，这种形式的字段被称为类型内嵌或匿名字段

类型内嵌的写法如下：
```
01   type Data struct {
02        int
03        float32
04        bool
05   }
06
07   ins := &Data{
08        int:      10,
09        float32: 3.14,
10        bool:     true,
11   }
```
代码说明如下：
- 第2～4行定义结构体中的匿名字段，类型分别是整型、浮点、布尔。
- 第8～10行将实例化的Data中的字段赋初值。

类型内嵌其实仍然拥有自己的字段名，只是字段名就是其类型本身而已，`结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能有一个`。结构体实例化后，如果匿名的字段类型为结构体，那么可以直接访问匿名结构体里的所有成员，这种方式被称为结构体内嵌。


## 3.1 结构内嵌特性
Go语言的结构体内嵌有如下特性。
### 3.1.1 内嵌的结构体可以直接访问其成员变量
嵌入结构体的成员，可以通过外部结构体的实例直接访问。如果结构体有多层嵌入结构体，结构体实例访问任意一级的嵌入结构体成员时都只用给出字段名，而无须像传统结构体字段一样，通过一层层的结构体字段访问到最终的字段。例如，ins.a.b.c的访问可以简化为ins.c。

### 3.1.2 内嵌结构体的字段名是它的类型名
内嵌结构体字段仍然可以使用详细的字段进行一层层访问，内嵌结构体的字段名就是它的类型名，
代码如下：
```
...

type people struct {
	Name, Sex      string
	Height, Weight float32
}

type boy struct {
	people
	Like  string
}

func RunStruct() {
	var b1 boy
	b1.Height = 175
	//详细的字段进行一层层访问
	fmt.Println(b1.people.Height)
}
```
一个结构体只能嵌入一个同类型的成员，无须担心结构体重名和错误赋值的情况，编译器在发现可能的赋值歧义时会报错。

## 3.2 使用组合描述对象特性

Go语言的结构体内嵌特性就是一种组合特性，使用组合特性可以快速构建对象的不同特性。
下面的代码使用Go语言的结构体内嵌实现对象特性组合，请参考代码6-10。
代码6-10 人和鸟的特性（具体文件：.../chapter06/humanbird/humanbird.go）
```
01   package main
02
03   import "fmt"
04
05  // 可飞行的
06   type Flying struct{}
07
08   func (f ＊Flying) Fly() {
09        fmt.Println("can fly")
10   }
11
12  // 可行走的
13   type Walkable struct{}
14
15   func (f ＊Walkable) Walk() {
16        fmt.Println("can calk")
17   }
18
19  // 人类
20   type Human struct {
21       Walkable                        // 人类能行走
22   }
23
24  // 鸟类
25   type Bird struct {
26       Walkable                        // 鸟类能行走
27       Flying                          // 鸟类能飞行
28   }
29
30   func main() {
31
32       // 实例化鸟类
33        b := new(Bird)
34        fmt.Println("Bird: ")
35        b.Fly()
36        b.Walk()
37
38       // 实例化人类
39        h := new(Human)
40        fmt.Println("Human: ")
41        h.Walk()
42
43   }

//运行结果
Bird:
can fly
can calk
Human:
can calk
```

代码说明如下：
- 第6行，声明可飞行结构（Flying）。
- 第8行，为可飞行结构添加飞行方法Fly()。
- 第13行，声明可行走结构（Walkable）。
- 第15行，为可行走结构添加行走方法Walk()。
- 第20行，声明人类结构。这个结构嵌入可行走结构（Walkable），让人类具备“可行走”特性
- 第25行，声明鸟类结构。这个结构嵌入可行走结构（Walkable）和可飞行结构（Flying），让鸟类具备既可行走又可飞行的特性。
- 第33行，实例化鸟类结构。
- 第35和36行，调用鸟类可以使用的功能，如飞行和行走。
- 第39行，实例化人类结构。
- 第41行，调用人类能使用的功能，如行走。

# 4. 注意事项

- 对于一个具体类型T，部分方法的接收者是T，而且其他方法的接收者是\*T。同时我们对类型`T的变量`直接调用接收者是\*T的方法是合法的，编译器隐私的帮你完成了取址的操作。但是不能直接通过类型调用

```
type DataWrite interface {
	Write(data interface{}) error
}

type fileHandle struct {

}

func (f *fileHandle)Write(data interface{}) error  {
	fmt.Println("文件写入中....")
	return nil
}

func RunInterface()  {
	// 通过变量可以直接调用接收者是*T
    var f fileHandle
	var _ = f.Write("...")

	// 不能直接调用接收者是*T
	// var _ = fileHandle{}.Write("..")
	/*
	此处代码会报错：
	...: cannot call pointer method on fileHandle literal
	...: cannot take the address of fileHandle literal
	*/

}
```
