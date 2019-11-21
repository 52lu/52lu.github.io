---
title: Go-基础篇-通道
date: 2018-05-08 13:53:15
tags:
 - go
categories:
 - 后端编程
---


# 1.为什么需要 channel？
 单纯地将函数并发执行是没有意义的。函数与函数间需要交换数据才能体现并发执行函数的意义。虽然可以使用共享内存进行数据交换，但是共享内存在不同的goroutine中容易发生竞态问题。为了保证数据交换的正确性，必须使用互斥量对内存进行加锁，这种做法势必造成性能问题。Go语言提倡使用通信的方法代替共享内存，这里通信的方法就是使用通道。
 
 
## 1.1 goroutine与channel的通信图
 
![](https://mrliuqh.github.io/directionsImg/go/goroutine%E4%B8%8Echannel%E7%9A%84%E9%80%9A%E4%BF%A1.png)
 
# 2. 通道的特性
 - 道是一种特殊的类型
 - 任何时候，同时只能有一个goroutine访问通道进行发送和获取数据
 
 - channle本质就是一个数据结构-队列
![](https://mrliuqh.github.io/directionsImg/go/gochannel.png)
- goroutine间通过通道就可以通信
- 通道像一个传送带或者队列，总是遵循先入先出（First In First Out）的规则，保证收发数据的顺序
- channel 是线程安全，多个协程操作同一个channel时，不会发生资源竞争问题（竞态）

# 3. 创建通道
## 3.1 创建无缓冲通道
`通道是引用类型，需要使用make进行创建`

```
通道实例 := make(chan数据类型)
```
- 数据类型：通道内传输的元素类型。
- 通道实例：通过make创建的通道句柄。

<b>使用格式</b>
```
// 创建一个整型类型的通道
intch := make(chan int)  

// 创建一个空接口类型的通道，可以存放任意格式
interfacech := make(chan interface{}) 

// 创建Mystruct指针类型的通道，可以存放＊Equip
type Mystruct struct{ 
  /* 一些字段 */ 
    ...
}

structch := make(chan ＊Mystruct)         
```

## 3.2 创建有缓冲通道
在无缓冲通道的基础上，为通道增加一个有限大小的存储空间形成带缓冲通道。带缓冲通道在发送时无需等待接收方接收即可完成发送过程，并且不会发生阻塞，只有当存储空间满时才会发生阻塞。同理，如果缓冲通道中有数据，接收时将不会发生阻塞，直到通道中没有数据可读时，通道将会再度阻塞。

<b>声明格式</b>
```
通道实例 := make(chan通道类型, 缓冲大小)
```
- 通道类型：和无缓冲通道用法一致，影响通道发送和接收的数据类型。
- 缓冲大小：决定通道最多可以保存的元素数量。
- 通道实例：被创建出的通道实例。

<b>使用示例</b>
```
func main() {
	//创建可以存放 3 map 类型通道
	intCh := make(chan int, 3)
	//数据发送到通道中
	intCh <- 34
	intCh <- 20
	intCh <- 10
	/*
	注意: 当我们给通道写入数据时，不能超过其容量,
	      否则报错:fatal error: all goroutines are asleep - deadlock!
	*/
	//intCh <- 1 

}
```
`当我们给通道写入数据时，不能超过其容量,否则报错:fatal error: all goroutines are asleep - deadlock!`

## 3.3 带缓冲通道阻塞条件
带缓冲通道在很多特性上和无缓冲通道是类似的。无缓冲通道可以看作是长度永远为0的带缓冲通道。因此根据这个特性，带缓冲通道在下面列举的情况下依然会发生阻塞。

- 带缓冲通道被填满时，尝试再次发送数据时发生阻塞。
- 带缓冲通道为空时，尝试接收数据时发生阻塞。

## 3.4 为什么对通道要限制长度？
我们知道通道（channel）是在两个goroutine间通信的桥梁。使用goroutine的代码必然有一方提供数据，一方消费数据。`当提供数据一方的数据供给速度大于消费方的数据处理速度时，如果通道不限制长度，那么内存将不断膨胀直到应用崩溃。`因此，限制通道的长度有利于约束数据提供方的供给速度，供给数据量必须在消费方处理量+通道长度的范围内，才能正常地处理数据。

## 3.5 单向通道声明
`只能发送的通道类型为: chan <- x，只能接收的通道类型为: x <- chan`

```
ch := make(chan int)
// 声明一个只能发送的通道类型，并赋值为ch
var ch Send Only chan<- int = ch

//声明一个只能接收的通道类型，并赋值为ch
var ch Recv Only <-chan int = ch

```

# 4.发送数据
`通道创建后，就可以使用特殊的操作符“<-”，向通道进行发送或者从通道接收数据。`

## 4.1 使用方法
```
func main() {
	//创建可以存放 3 map 类型通道
	intCh := make(chan int, 3)
	//数据发送到通道中
	intCh <- 34
	intCh <- 20
	intCh <- 10
	/*
	注意: 当我们给管写入数据时，不能超过其容量,
	      否则报错:fatal error: all goroutines are asleep - deadlock!
	*/
	//intCh <- 1 

}

```


# 5. 接收数据
## 5.1 阻塞接收数据
`阻塞模式接收数据时，将接收变量作为“<-”操作符的左值，格式如下:`
```
data := <-ch
```
> 执行该语句时将会阻塞，直到接收到数据并赋值给data变量

## 5.2 非阻塞接收数据
`使用非阻塞方式从通道接收数据时，语句不会发生阻塞，格式如下：`
```
data, ok := <-ch
```
- data：表示接收到的数据。未接收到数据时，data为通道类型的零值。
- ok：表示是否接收到数据。
>非阻塞的通道接收方法可能造成高的CPU占用，因此使用非常少。如果需要实现接收超时检测，可以配合select和计时器channel进行。

## 5.3 接收任意数据，忽略接收的数据
`阻塞接收数据后，忽略从通道返回的数据，格式如下：`
```
<-ch
```
> 执行该语句时将会发生阻塞，直到接收到数据，但接收到的数据会被忽略。<font color=ffb800>这个方式实际上只是通过通道在goroutine间阻塞收发实现并发同步。</font>


- 使用示例
```
func main() {
	intCh := make(chan int)
	start := time.Now()
	testNum := 10
	go func() {
		fmt.Println("start goroutine....")
		for i := 0; i < 3; i++ {
			testNum += i
			time.Sleep(time.Second)
		}
		//数据写入通道
		intCh <- testNum
		fmt.Println("end goroutine....")
	}()

	//等待匿名函数执行完成
	fmt.Println("wait goroutine...")
	//执行该语句时将会发生阻塞，直到接收到数据，但接收到的数据会被忽略
	<-intCh
	diff := time.Now().Sub(start)
	fmt.Printf("testNum: %d\n", testNum)
	fmt.Println("耗时: ", diff)
}
```


# 6. channel 使用的注意事项

- channle的容量放满后，就不能再放入了

- channel中只能存放指定的数据类型
- 空接口类型的通道能接收任意参数

```
interfaceCh := make(chan interface{}, 4)
//发送字符串
interfaceCh <- "hello"
//发送整型
interfaceCh <- 100
//发送切片
interfaceCh <- []int{1, 2, 3, 4}

/*
range函数遍历每个从通道接收到的数据，因为queue再发送完3个数据之后就关闭了通道，所以这里我们range函数在接收到3个数据之后就结束了。
如果上面的queue通道不关闭，那么range函数就不会结束，从而在接收第4个数据的时候就阻塞了。
 */
close(interfaceCh)
for data := range interfaceCh {
    fmt.Println(data)
}
```
- 发送将持续阻塞直到数据被接收

- 通过range函数遍历通道接收数据时，要再发送完数据到通道后，用Close()函数显示的关闭通道，否则range函数就不会结束
- 通道一次只能接收一个数据元素

# 7. 使用select多路复用 
`在使用通道时，想同时接收多个通道的数据是一件困难的事情。通道在接收数据时，如果没有数据可以接收将会发生阻塞。`

虽然可以使用如下模式进行遍历，但运行性能会非常差。
```
//运行性能会非常差
for{
    // 尝试接收ch1通道
    data, ok := <-ch1
    // 尝试接收ch2通道
    data, ok := <-ch2
    // 接收后续通道
    …
}
```

`Go语言中提供了select关键字，可以同时响应多个通道的操作。select的每个case都会对应一个通道的收发过程。当收发完成时，就会触发case中响应的语句。多个操作在每次select中挑选一个进行响应。`

## 7.1 声明格式
格式如下：
```
select{
  case 操作1:
      //响应操作1
  case 操作2:
      //响应操作2
       …
  default:
    //没有操作情况
}

```
- case <- ch: 代表接收任意数据
 
- case d:= <-ch: 接收变量

- case ch <- 120: 发送数据到通道

## 7.2 使用示例 
```
//生成通道int ch
intCh := make(chan int, 10)
for i := 1; i < 10; i++ {
	intCh <- i
}
//生成通道string ch
strCh := make(chan string, 10)
for i := 1; i < 10; i++ {
	strCh <- "String:" + fmt.Sprintf("%d", i)
}
/*
传统的方法在遍历管道时，如果不关闭会阻塞,从而导致deadlock，在实际开发中，可能我们不好确定什么关闭该管道,
 可以使用 select 方式可以解决
*/
//使用select
for {
	select {
	case v := <-intCh:
		fmt.Println("intCH: ", v)
	case v := <-strCh:
		fmt.Println("strCh: ", v)
	default:
		fmt.Println("Noting!")
		return
	}
}
```