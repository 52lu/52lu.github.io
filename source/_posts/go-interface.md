---
title: Go接口
date: 2018-05-28 11:31:57
tags:
 - go
categories:
 - go
---

> Go 语言不是一种 “传统” 的面向对象编程语言：它里面没有类和继承的概念。但是 Go 语言里有非常灵活的**接口**概念，通过它可以实现很多面向对象的特性

### 1. 什么是interface(接口)
`简单地说，interface是一组method的组合，但是这些method不包含（实现）代码,我们通过interface来定义对象的一组行为。`
> 接口里不能包含变量

### 2. 接口声明格式
```
type Namer interface {
    Method1(param_list) return_type
    Method2(param_list) return_type
    ...
}
```
<!--more-->

上面的 Namer 是一个 **接口类型**。

- <font color=red>按照约定,只包含一个方法的接口的名字,由方法名加 [e]r 后缀组成</font>，例如 Printer、Reader、Writer、Logger、Converter 等等。还有一些不常用的方式（当后缀 er 不合适时），比如 Recoverable，此时接口名以 able 结尾，或者以 I 开头。
- 当方法名首字母是大写时，且这个接口类型名首字母也是大写时，这个方法可以被接口所在的包（package）之外的代码访问。
- 参数列表、返回值列表：参数列表和返回值列表中的参数变量名可以被忽略

- Go 语言中的接口都很简短，通常它们会包含 0 个、最多 3 个方法

- 接口里的所有方法都没有方法体，即接口的方法都是没有实现的方法。接口体现了程序设计的
多态和高内聚低偶合的思想

- Golang中的接口，不需要显式的实现。只要一个变量，含有接口类型中的所有方法，那么这个
变量就实现这个接口。因此，Golang 中没有 implement 这样的关键字


### 3. 接口嵌套(接口继承)
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


### 4. 接口使用中的注意事项
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