---
title: Go学习笔录
date: 2018-04-03 16:48:41
tags:
 - go
categories:
 - go
---

## 1. 基础组成部分
- 包声明
- 引入包
- 函数
- 变量
- 语句 & 表达式
- 注释

以实际代码进行讲解:
```
/* 这是包声明 */
package main

/* 这是引入包 */
import "fmt"

/* 这是函数 */
func main() {
  /* 这是变量 */
  str := "hello word"
  /* 这是语句 */
  fmt.Println(str)
}

```
- 第一行代码 package main 定义了包名。你必须在源文件中非注释的第一行指明这个文件属于哪个包。
> 每个 Go 应用程序都包含一个名为 main 的包

- import "fmt" 告诉 Go 编译器这个程序需要使用 fmt 包
- func main() 是程序开始执行的函数。main 函数是每一个可执行程序所必须包含的，一般来说都是在启动后第一个执行的函数。
>如果有 init() 函数则会先执行init()函数
<!--more-->
- go语言中注释 单行用:// ，多谢 /\*...*/  或者: 
```go
func main()  {
	// str := "hello word"
	fmt.Println(str)
}
```
- go语言中一行代表一个语句结束。每个语句不需要像 C 家族中的其它语言一样以分号分号(;) 结尾。

## 2. 程序设计规则
Go语言之所以简洁，是因为它有一些默认的行为。
- 大写字母开头的变量是可导出的，即其他包可以读取，是公用变量；小写字母开头的不可导出，是私有变量。
- 大写字母开头的函数也是一样，相当于class中带public关键词的公有函数；小写字母开头就是有private关键词的私有函数。


## 3. 数据类型

> **在GO中字符串是用双引号("")或反引号(``)括起来，而且字符串不可变**

#### 3.1.1 字符串操作
- 修改字符串
直接修改字符串报错,如下代码:
```go
var s string = "hello word"
s[0] =`a`

//报错
# command-line-arguments
...cannot assign to str[0]
```
通过转[]byte类型修改字符串
```go
var s string = "hello word"
slice := []byte(s)
slice[0] ='H'
newStr := string(slice)
fmt.Println(newStr)
```

> <font color=red> 此处的 slice[0] ='H' 不能写成 slice[0] ="H",否则会报错:cannot use "H" (type string) as type byte in assignment </font>


- 字符串转[]byte 类型
```go
var s string = "hello word"
sic := []byte(s)
```

- 字符串转整型
```go
n, err := strconv.Atoi("12")
if err != nil {
	fmt.Println("转换失败")
}
fmt.Println(n)
```

- 字符串连接
```go
//字符串连接
a, b := "hello", " word"
c := a + b
fmt.Println(c)
```
- 声明多行字符串
```go
str := ` hello,
        nice to meet you`
```
> `` 括起的字符串为Raw字符串，即字符串在代码中的形式就是打印时的形式，它没有字符转义，换行也将原样输出。

#### 3.1.2 字符串常用函数
>处理字符串的函数主要集中在strings和strconv

- 字符串拼接和分割

<font color=red>使用函数:strings.Join 和 strings.Split</font>
```
//切片转 以x拼接成 字符串
fmt.Println("Join: ", strings.Join([]string{"a", "b", "c"}, "-"))
//输出：Join:  a-b-c

// 字符串 以x分割成 切片
fmt.Println("Split: ", strings.Split("a-b-c-d-e", "-"))
//输出：Split:  [a b c d e]


// 去除字符串s中的空格符, 并按照空格(可以是一个或者多个空格)分割字符串, 返回slice
func Fields(s string) []string

str := "nice to meet   you"
slice := strings.Fields(str) // [nice to meet you]
```
- 字符串比较

<font color=red>使用函数:strings.Compare</font>
```go

fmt.Println(strings.Compare(string("hello"), string("haha")))  // 1
fmt.Println(strings.Compare(string("hello"), string("helloworld")))  // -1
fmt.Println(strings.Compare(string("hello"), string("hello")))  //0

// Compare函数:源码
func Compare(a, b string) int {
	if a == b {
		return 0
	}
	if a < b {
		return -1
	}
	return +1
}
```
- 字符串查找

<font color=red>使用函数:strings.Contains、Index、LastIndex、Count</font>

```
// 判断给定字符串s中是否包含子串substr, 找到返回true, 找不到返回false
func Contains(s, substr string) bool

fmt.Println(strings.Contains("hello word", "hell"))  // true
fmt.Println(strings.Contains("hello word", "hella"))  // false
fmt.Println(strings.Contains("", ""))  // true

// 在字符串s中查找sep第一次出现的位置, 返回位置值, 找不到返回-1
func Index(s, sep string) int

// 在字符串s中查找sep最后一次出现的位置, 返回位置值, 找不到返回-1
func LastIndex(s, sep string) int

fmt.Println(strings.Index("abcdefga", "a")) //0
fmt.Println(strings.LastIndex("abcdefga", "a"))//7


// 统计给定子串sep的出现次数, sep为空时, 返回1 + 字符串的长度
func Count(s, sep string) int

fmt.Println(strings.Count("hello", "l"))//2
fmt.Println(strings.Count("hello", ""))//6
```

- 字符串重复
```
// 重复s字符串count次, 最后返回新生成的重复的字符串
func Repeat(s string, count int) string

fmt.Println(strings.Repeat("hello", 2)) //hellohello
```

- 字符串大小写转换
```
// 首字母大写
func Title(s string) string

fmt.Println(strings.Title("hello")) //Hello


// 所有字母转换为小写
func ToLower(s string) string

fmt.Println(strings.ToLower("HELLO")) //hello


// 所有字母转换为大写
func ToUpper(s string) string

fmt.Println(strings.ToUpper("hello")) //HELLO
```
- 字符串前缀后缀
> <font color=red>大小写敏感</font>

```
// 判断字符串是否包含前缀prefix
func HasPrefix(s, prefix string) bool

fmt.Println(strings.HasPrefix("Golang", "Go"))  // true
fmt.Println(strings.HasPrefix("Golang", "go"))  // false
fmt.Println(strings.HasPrefix("Golang", "a"))  // false
fmt.Println(strings.HasPrefix("Golang", ""))  // true


// 判断字符串是否包含后缀suffix, 
func HasSuffix(s, suffix string) bool

fmt.Println(strings.HasSuffix("Golang", "ang"))  // true
fmt.Println(strings.HasSuffix("Golang", "Ang"))  // false
fmt.Println(strings.HasSuffix("Golang", "bng"))  // false
fmt.Println(strings.HasSuffix("Golang", ""))  // true
```

- 字符串删除
```
// 删除在s字符串的头部和尾部中由cutset指定的字符, 并返回删除后的字符串
func Trim(s string, cutset string) string

fmt.Println(strings.Trim("@hello word@","@")) // hello word
```

- 字符串替换
```
// 在s字符串中, 把old字符串替换为new字符串，n表示替换的次数，小于0表示全部替换
func Replace(s, old, new string, n int) string


fmt.Println(strings.Replace("hello word","o","@",1)) // hell@ word
fmt.Println(strings.Replace("hello word","o","@",-1)) // hell@ w@rd
```

### 3.2 数组操作
