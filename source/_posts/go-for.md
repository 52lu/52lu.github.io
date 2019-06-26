---
title: Go遍历使用
date: 2018-06-03 11:31:57
tags:
 - go
categories:
 - go
---

### 1. 遍历字符串(string)

#### 1.1 统计长度后遍历
```
func ForString() {
	str := "hello word"
	//1.统计长度后遍历
	length := len(str)
	for i := 0; i < length; i++{
		fmt.Printf("字符: %c ",str[i]) 
	}
}
```
#### 1.2 转为byte后遍历
```
func ForString() {
	str := "hello word"
	strByte := []byte(str)
	for _,v := range strByte{
		fmt.Printf("字符: %c ",v)
	}
}
```
<!--more-->
### 2. 遍历数组(array)
#### 2.1 遍历一维数组
```
func ForArray() {
	//遍历一维数组
	arr := [5]int{1, 2, 3, 4, 5}
	for _, v := range arr {
		fmt.Printf("元素: %v ", v)
	}
}
```
#### 2.2 遍历二维数组
```
func ForArray() {
	//遍历二维数组
	arr2 := [2][5]int{{1, 2, 3, 4, 5}, {6, 7, 8, 9, 10}}
	fmt.Println(arr2)
	for _, arr := range arr2{
		for _, v := range arr{
			fmt.Printf("v: %v ", v)
		}
	}
}
```
### 3. 遍历切片(slice)

#### 3.1 遍历一维切片
```
func ForSlice() {
	//遍历一维切片
	slice := []string{"张三", "李四", "王五", "费六"}
	for _, v := range slice {
		fmt.Println("姓名:", v)
	}
}
```

#### 3.2 遍历二维切片
```
func ForSlice() {
	//遍历多维切片
	slice2 := [][]string{
		{"张三", "李四", "王五", "费六"},
		{"小花", "小兰", "小雨", "小丽"},
	}
	for _, slice := range slice2{
		// 循环嵌套
		for _,name := range slice{
			fmt.Println("姓名:", name)
		}
	}
}

```

### 4. 遍历映射（map）
#### 4.1 遍历一维map
```
type Student struct {
	ClassName string
	Fraction  int
}
func ForMap() {
	m := map[string]Student{
		"张三": Student{"一A年级", 68},
		"李速": Student{"一B年级", 72},
		"小丽": Student{"一A年级", 65},
	}
	for name, stu := range m {
		fmt.Println("姓名:", name, "班级:", stu.ClassName, "分数:", stu.Fraction)
	}

}

```
#### 4.2 遍历多维map
```
type Student struct {
	ClassName string
	Fraction  int
}

func ForMap() {
	// 遍历多维map
	m2 := map[int]map[string]Student{}
	m2[1] = map[string]Student{
		"李速": Student{"一B年级", 72},
	}
	m2[2] = map[string]Student{
		"张三": Student{"一A年级", 68},
	}
	m2[3] = map[string]Student{
		"小丽": Student{"一A年级", 65},
	}

	for sort, m := range m2 {
		for name,stu := range m {
			fmt.Println("排名:",sort,"姓名:", name, "班级:", stu.ClassName, "分数:", stu.Fraction)
		}
	}
}
```

### 5.遍历结构（struct）
#### 5.1 常结构遍历
```
type People struct {
	Name string
	Age int
	Sex string
}

func ForStruct()  {
	boy := People{"李四", 20, "男"}
	v := reflect.ValueOf(boy)
	t := reflect.TypeOf(boy)
	for i := 0; i < t.NumField() ; i++  {
		fmt.Println("字段名:",t.Field(i).Name,"字段值:",v.Field(i).Interface())
	}
}
/**
输出:
*/
```
#### 5.2 嵌套结构遍历
```
type People struct {
	Name string
	Age  int
	Sex  string
}

type Girl struct {
	Like string
	People
}

func ForStruct() {
	//嵌套结构体
	g := Girl{"唱歌", People{"小花", 18, "女"}}
	gv := reflect.ValueOf(g)
	gt := reflect.TypeOf(g)
	for i := 0; i < gv.NumField(); i++ {
		//判断是否是结构体
		if gv.Field(i).Type().Kind() == reflect.Struct {
			structFiled := gv.Field(i).Type()
			for j := 0; j < structFiled.NumField(); j++ {
				fmt.Println("嵌套结构==>", "字段名:", structFiled.Field(i).Name, "字段值:", gv.Field(i).Field(j).Interface())
			}
			continue
		}
		fmt.Println("外层结构==>", "字段名:", gt.Field(i).Name, "字段值:", gv.Field(i).Interface())
	}
}
```