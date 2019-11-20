---
title: 学Go笔记-函数使用
date: 2018-04-08 11:31:57
tags:
 - go
categories:
 - 后端
---

函数使用
> 函数是Go语言里面的核心设计，它通过关键字func来声明，它的格式如下。

### 1. 函数声明
```
func funcName(input1 type1, input2 type2) (output1 type1, output2 type2) {
   //这里是处理逻辑代码
   
   //返回多个值 

   return value1, value2
 }
```
* 关键字func用来声明一个函数funcName。

<!--more-->
- 函数可以有一个或者多个参数，每个参数后面带有类型，通过“,”分隔函数可以返回多个值。

- 返回值声明了两个变量output1和output2，如果你不想声明也可以，就保留两个类型声明。

- 如果只有一个返回值且不声明返回值变量，那么你可以省略“包括返回值”的括号

- 如果没有返回值，就直接省略最后的返回信息。

-  如果有返回值，那么必须在函数的外层添加return语句。

### 2. 接收变参的函数
```
func funcName(input ...type) (output1 type1, output2 type2) {
   //这里是处理逻辑代码
   
   //返回多个值 

   return output1 type1, output2 type2
 }
```

> 注意，这些参数的类型全部是type。在函数体中，变量input是一个type的slice。


- 使用示例

```
package main

import "fmt"

func main() {
   result := joinStr("h", "e", "l", "l", "o")
   fmt.Println(result) // hello
}

func joinStr(input ...string) (strRes string) {
   for _, v := range input {
      strRes += v
   }
   return
}
```

### 3.  参数传递
> Go 默认使用按值传递参数,也就是传递参数的副本。函数接收参数副本之后，在使用变量的过程中可能对副本的值进行更改，但不会影响到原来的变量。如果想要影响到原来的变量，则需要传指针。<font color=red>需要注意的是:切片（slice）、字典（map）、接口（interface）、通道（channel）这样的引用类型都是默认使用引用传递（即使没有显示的指出指针）</font>

#### 3.1  按值传递使用示例
```
package main

import "fmt"

func main() {
   str := "good morning"
   updateStr(str)
   fmt.Println(str) // good morning
}
func updateStr(str string) string {
   str = "good evening"
   return str
}
```

#### 3.2  按引用使用示例
```
package main

import "fmt"

func main() {
   str := "good morning"
   updateStr(&str)
   fmt.Println(str)  // good evening
}
func updateStr(str *string) string {
   *str = "good evening"
   return *str
}
```

### 4.延迟语句（defer）的使用
>Go语言中有种不错的设计，即延迟（defer）语句，你可以在函数中添加多个defer语句。当函数执行到最后时，这些 defer 语句会按照逆序执行，最后该函数返回。特别是当你在进行一些打开资源的操作时，遇到错误需要提前返回，在返回前你需要关闭相应的资源，不然很容易造成资源泄露等问题。

#### 4.1 使用示例
```
package main

import (
	"fmt"
	"os"
	"io/ioutil"
)

func main() {
	filePath := "/Users/liuqh/Desktop/test.json"
	str := openFile(filePath)
	fmt.Println(str) 
	//输出以下内容
	//defer 被调用了
    //文件里的内容
}

func openFile(filePath string) string {
	//打开文件
	file, err := os.Open(filePath)
	if err != nil {
		fmt.Println("open file err")
	}
	//利用defer 关闭文件
	defer testDefer(file)
	content, err := ioutil.ReadAll(file)
	if err != nil {
		fmt.Println("read file err")
	}
	return string(content)
}

func testDefer(file *os.File) {
	fmt.Println("defer 被调用了")
	file.Close()
}
```

#### 4.2 函数中使用多个defer
> 如果有一个函数中有多个地方调用defer,那么defer采用后进先出(逆序执行)

```
package main

import (
	"fmt"
)

func main() {
	testDefer() // 5 4 3 2 1
}

func testDefer() {
	for i := 1; i < 6; i++ {
		defer fmt.Println(i)
	}
}
```

### 5.函数作为值、类型
>在Go语言中函数也是一种变量，我们可以通过type来定义它，它的类型就是所有拥有相同的参数，相同的返回值。

``` go
type typeName func(input1 inputType1,input2 inputType2[,...])(result1 resultType1 [, ...])
```
#### 5.1 使用示例
```
package main

import "fmt"

type testInt func(int) bool

func main() {
	numSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	odd := filter(numSlice, isOdd)
	event := filter(numSlice, isEvent)
	fmt.Println(odd) // [2 4 6 8]
	fmt.Println(event) // [1 3 5 7 9]
}

func isOdd(num int) bool {
	if num%2 == 0 {
		return true
	}
	return false
}

func isEvent(num int) bool {
	if num%2 == 0 {
		return false
	}
	return true
}

func filter(arg []int, funcName testInt) []int {
	var result []int
	for _, v := range arg {
		if funcName(v) {
			result = append(result, v)
		}
	}
	return result
}

```
### 6.匿名函数
既没有名称的函数

#### 6.1 使用示例
```
package main

import "fmt"

func main()  {
	f := func(a,b int) int{
		i := a + b
		return i
	}
	fmt.Println(f(1,4)) // 5 
}
```

