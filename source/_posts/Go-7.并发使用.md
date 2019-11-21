---
title: Go-基础篇-并发使用
date: 2018-05-07 18:20:22
tags:
 - go
categories:
 - 后端编程
---

# 1. 并发和并行
## 1.1 并发
多线程程序在单核上运行，就是并发
### 1.1.1 并发的特点
- 多个任务作用在一个cpu
- 从微观角度看，在一个时间点上，只有一个任务在执行

## 1.2 并行
多线程程序在多核上运行，就是并行
### 1.2.1 并行的特点
- 多个任务作用在多个cpu
- 从微观角度看，在一个时间点上，有多个任务在执行




# 2. Go 协程和 Go 主线程
## 2.1 关系介绍
- 主线程是一个物理线程，直接作用在 cpu 上的。是重量级的，非常耗费 cpu 资源。
- 协程从主线程开启的，是轻量级的线程，是逻辑态。对资源消耗相对小。
- Go的协程机制是重要的特点，可以轻松的开启上万个协程。其它编程语言的并发机制是一
般基于线程的，开启过多的线程，资源耗费大，这里就突显 Go在并发上的优势了

## 2.2 Go主进程和Go协程（goroutine）关系示意图
![](https://mrliuqh.github.io/directionsImg/go/go%E4%B8%BB%E7%BA%BF%E7%A8%8B%E5%92%8C%E5%8D%8F%E7%A8%8B.png)

## 2.3 Go协程的特点
- 有独立的栈空间
- 共享程序堆空间
- 调度由用户控制
- 协程是轻量级的线程


# 3. Go协程（Goroutine）的使用
Go程序中使用go关键字为一个函数创建一个goroutine。一个函数可以被创建多个goroutine，一个goroutine必定对应一个函数。

## 3.1 使用方式
### 3.1.1 普通函数创建goroutine
<b>使用格式</b>
```
go 函数名 ( 参数列表 )
```
-  函数名：要调用的函数名。
- 参数列表：调用函数需要传入的参数。
><font color=red>使用go关键字创建goroutine时，被调用函数的返回值会被忽略。</font>如果需要在goroutine中返回数据，则需要通过通道（channel）把数据从goroutine中作为返回值传出。

<b>使用示例</b>

```
package main

import (
	"fmt"
	"time"
	"strconv"
)

func test() {
	var i =1
	for {
		fmt.Println(""+strconv.Itoa(i))
		time.Sleep(time.Second)
		i++
	}
}
func main() {
    // 为一个普通函数创建goroutine
	go test()
	//接收用户输入,知道按Enter键时
	var input string
	//将用户输入内容写入input变量中，并返回，整个程序终止
	fmt.Scanln(&input)
	
}
```

输出：
```
1
2
3
4
5
6
...

exit
```
> <font color=red>所有goroutine在mian()函数结束时会一同结束</font>

### 3.1.2 匿名函数创建goroutine
`go关键字后也可以为匿名函数或闭包启动goroutine`

<b>使用格式</b>
`使用匿名函数或闭包创建goroutine时，除了将函数定义部分写在go的后面之外，还需要加上匿名函数的调用参数`

```
go func( 参数列表 ){
         函数体
}( 调用参数列表 )
```
- 参数列表：函数体内的参数变量列表。
- 函数体：匿名函数的代码。
- 调用参数列表：启动goroutine时，需要向匿名函数传递的调用参数

<b>使用示例</b>

```
package main

import (
	"fmt"
	"time"
)
func main() {
	go func() {
		var i int
		for {
			fmt.Println("计时器:", i)
			time.Sleep(time.Second)
			i++
		}
	}()
	var input string
	fmt.Scanln(&input)
}
```

## 3.2 并发运行性能调整
### 3.2.1 设置运行的 cpu 数
`为了充分了利用多 cpu 的优势，在 Golang 程序中，可以通过runtime.GOMAXPROCS() 函数 设置运行的 cpu 数目`

<b>使用格式:</b>
```
runtime.GOMAXPROCS( 逻辑CPU)
//逻辑CPU可以通过 runtime.NumCpu()函数获取
```

这里的逻辑CPU数量可以有如下几种数值:

- 逻辑CPU < 1：不修改任何数值
- 逻辑CPU = 1：单核心执行
- 逻辑CPU > 1：多核并发执行

<b>注意事项:</b>
- Go 1.5版本之前，默认使用的是单核心执行。从Go 1.5版本开始，默认执行上面语句以便让代码并发执行，最大效率地利用CPU。
- GOMAXPROCS同时也是一个环境变量，在应用程序启动前设置环境变量也可以起到相同的作用。