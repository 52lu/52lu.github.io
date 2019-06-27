---
title: Go错误处理
date: 2018-05-29 11:31:57
tags:
 - go
categories:
 - go
---
[toc]
> Go 语言不支持传统的 try...catch...finally,取代的方法是:Go 中可以抛出一个 panic 的异常，然后在 defer 中
通过 recover 捕获这个异常，然后正常处理

### 1.概念词介绍
#### 1.1 Panic
> 是一个内建函数，可以中断原有的控制流程，进入一个令人恐慌的流程 中。当函数 F 调用 panic，函数 F 的执行被中断，并且 F 中的延迟函数会正 常执行，然后 F 返回到调用它的地方。在调用的地方， F 的行为就像调用 了 panic。这一过程继续向上，直到程序崩溃时的所有 goroutine 返回。
恐慌可以直接调用 panic 产生。也可以由运行时错误产生，例如访问越界 的数组

#### 1.2 Recover
> 是一个内建的函数，可以让进入令人恐慌的流程中的 goroutine 恢复过 来。recover 仅在延迟函数（defer）中有效。在正常的执行过程中，调用 recover 会返回 nil 并且没有其他任何效果。 如果当前的 goroutine 陷入恐慌，调用 recover 可以捕获到 panic 的输入值， 并且恢复正常的执行。

<!--more-->

####  1.3 使用 defer+recover+panic

```
package study

import "fmt"

func RunError() {
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println(err)
		}
	}()
	test(10, 0)
	fmt.Println("错误后执行的代码")
}

func test(num1, num2 int) {
	//fmt.Println(num1 / num2)
	panic("error: 这里报错了,除数不能为0！")
}
/**
输出:

error: 这里报错了,除数不能为0！

Process finished with exit code 0
*/
```

### 2. 自定义错误
**Go 程序中，也支持自定义错误， 使用 errors.New 和 panic 内置函数。**
- errors.New("错误说明") , 会返回一个 error 类型的值，表示一个错误
- panic 内置函数 ,接收一个 interface{}类型的值(也就是任何值了)作为参数。可以接收 error 类
   型的变量，输出错误信息，并退出程序

#### 2.1 使用示例
```

package study

import (
	"fmt"
	"errors"
)

func RunError() {
	err := selfError()
	if err != nil{
		panic(err) //抛出错误
	}
	fmt.Println("错误后执行的代码")
}

func selfError()  error {
	return errors.New("error:我自己定义的错误")
}

/*
输出:


panic: error:我自己定义的错误

goroutine 1 [running]:
study.RunError()
	/Users/liuqh/go/src/study/error.go:18 +0xd8
main.main()
	/Users/liuqh/go/src/study.go:6 +0x20
*/
```