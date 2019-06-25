---
title: Go结构体使用
date: 2018-05-10 11:31:57
tags:
 - go
categories:
 - go
---

>结构体（struct）是Go支持面向对象编程特性的基础



### 1.声明结构体
`结构体的定义只是一种内存布局的描述，只有当结构体实例化时，才会真正地分配内存。因此必须在定义结构体并实例化后才能使用结构体的字段。
实例化就是根据结构体定义的格式创建一份与格式一致的内存区域，结构体实例与实例间的内存是完全独立的。`

#### 1.1 基本语法
```
type 结构体名称 struct {
   field type
   field type 
}

```
> <font color=red>字段直接没有逗号（,）

- field: 从概念或叫法上称: 结构体字段、属性 
- type：一般是基本数据类型、数组,也可是引用类型

<!--more-->

- ##### 同类型变量可以写在一行
```
type Human struct {
	name string
	age,sex,height int
}
```

### 2.结构体实例化
#### 2.1 基本实例化形式（使用关键字: var）
```
type Girl struct {
	Name  string 
}
func UseStruct() {
	//使用关键字var
	var g Girl
	g.Name = "小芳"
	fmt.Println(g) // 输出: {小芳 0 0 0}
}
```
> 上述变量 g 为结构体的实例

#### 2.2 创建指针类型的结构体（使用关键字: new）
```
type Girl struct {
	Name  string 
}
func UseStruct() {
  // 使用new
	g1 := new(Girl)
	g1.Name = "小芳"
	fmt.Println(g1) // 输出: &{小芳 0 0 0}
}
```
> Girl类型被实例化后保存到g1变量中，g1的类型为*Girl，属于指针

> <font color=red>经过new实例化的结构体实例在成员赋值上与基本实例化的写法一</front>

#### 2.3 去结构的地址实例化（使用: &）
**在Go语言中，对结构体进行“&”取地址操作时，视为对该类型进行一次new的实例化操作**
```
type Girl struct {
	Name  string 
}
func UseStruct() {
  // 使用&
	g2 := &Girl{}
	g2.Name = "小芳"
	fmt.Println(g2) // 输出: &{小芳 0 0 0}
}
```

### 3. 初始化结构体字段用例
#### 3.1 递归填充
```
type Family struct {
	name  string
	child *Family
}

func UseStruct() {
	p := Family{
		name: "爷爷",
		child: &Family{
			name: "爸爸",
			child: &Family{
				name: "我",
			},
		},
	}
	fmt.Println(p.name) // 爷爷
	fmt.Println(p.child.name) //爸爸 
	fmt.Println(p.child.child.name) // 我
}
```
#### 3.2 多值初始化
```
type Girl struct {
	Name   string 
	Age    int
	Height int
	Weight float32
}
func CreateStruct() {
    // 顺序填充
	g1 := Girl{"小花", 18, 170, 55.4}
	fmt.Println(g1) // {小花 18 170 55.4}
	
	// 指定字段填充
	g := Girl{Name:"小雨", Age:19}
	fmt.Println(g) // {小雨 19 0 0}
} 
```
#### 3.2 匿名结构体初始化
`结构体可以包含一个或多个 匿名（或内嵌）字段，即这些字段没有显式的名字，只有字段的类型是必须的，此时类型就是字段的名字。匿名字段本身可以是一个结构体类型，即 结构体可以包含内嵌结构体`

```
package main

import "fmt"

type Human struct {
	Name     string
	Age, Sex int
}
type Girl struct {
	Human //匿名字段
	Like string
}

func main() {
	p := Girl{Human{Name: "小芳", Age: 18}, "唱歌"}
	fmt.Println(p)
}

```


### 4. 带标签的结构体
`以是文档或其他的重要标记。标签的内容不可以在一般的编程中使用，只有reflect(反射) 能获取它。`

#### 4.1 使用示例
```
package main

import (
	"reflect"
	"fmt"
)

type SchoolTag struct {
	Name    string `school name`
	Address string `school address`
	Tel     string `school tel`
}

func main() {
	schTag := SchoolTag{"名牌大学", "天朝1号", "010-1234560"}
	count := reflect.ValueOf(schTag).NumField()
	for i := 0; i < count; i++ {
		fmt.Printf("%v\n",reflect.TypeOf(schTag).Field(i).Tag)
	}
}

```


### 5. 结构体比较
`如果结构体的所有成员变量都可以比较，那么这个结构体就是可比较的。两个结构体的比较用 == 或者 != 其中== 按照顺序比较两个结构体变量的成员`
```
package main

import "fmt"

type Point struct {
	X, Y int
}
func main() {
	p1 := Point{1,2}
	p2 := Point{1,3}
	//表达式1
	fmt.Println(p1.X == p2.X && p1.Y == p2.Y) //false
	//表达式2
	fmt.Println(p1 == p2) //false
}
```
> 表达式1和表达式2 是等价的

**可比较的结构体都可以作为map的键类型**

示例（统计某个链接的点击次数):
```
package main

import "fmt"

type UrlCount struct {
	Controller, Action string
}

func main() {
	m := make(map[UrlCount]int)
	for i := 0; i < 5; i++ {
		m[UrlCount{"home", "index"}]++
	}
	fmt.Println(m) // map[{home index}:5]
}

```

### 6 结构体特性
- 在创建一个结构体变量后，如果没有给字段赋值，都对应一个零值(默认值)

- 结构体是值类型，默认为值拷贝
- 字段名必须唯一
- 结构体的所有字段在内存中是连续的
- 结构体是用户单独定义的类型，和其它类型进行转换时需要有完全相同的字段(名字、个数和类型)
```
package main

import "fmt"

type A struct {
	X, Y int
}
type B struct {
	X, Y int
}

func main() {
	var a A
	var b B
	a = A(b) // 类型转换时，需要有完全相同的字段(名字、个数和类型)
	fmt.Printf("%T \n",a)
	fmt.Printf("%T \n",b)
}
```
- 结构体进行 type 重新定义(相当于取别名)，Go 认为是新的数据类型，但是相互间可以强转
```
package main

type Human struct {
	Name string
	Age  int
}
type Boy Human

func main() {
	var h Human
	var b Boy
	//这么写错误,原因:Go认为Boy是新的数据类型
	// h == b
	//可以相互间强转
	b = Boy(h)
	h = Human(b)
}
```



### 7.结构体和结构体变量(实例)的区别和联系
- 结构体是自定义的数据类型，代表一类事物.
- 结构体变量(实例)是具体的，实际的，代表一个具体变量