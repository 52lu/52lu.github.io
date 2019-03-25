---
title: PHP中的in_array($val,$array,$accurate)，第三个参数$accurate
date: 2016-07-01 10:25:05
tags:
 - php
categories:
 - php
---

问题描述:
 在使用php的in_array 函数时；我习惯性使用以下的方式判断：
 ```$xslt
 $a     = '1abc';
 $array =  [1,2,3];
 if (in_array($a,$array){
     echo  'do .....';
 } else {
     echo  'do else .....';
 }

```
<!--more-->
按照预想应该会输出
```$xslt
do else .....

```

但是实际输出的是：
```$xslt
do  .....

```

原因：根据官方文档函数 in_array 的描述：
![avatar](/images/php-in-array.png)


在 array 中搜索 search，如果没有设置 type 则使用宽松的比较。
第三个参数默认为 false，当 search 和 array 中的元素比较时会进行类型转换，相当于使用 == 进行比较。

<font color=#DC143C>备注：上个例子中的$a='1abc' 再和数字做比较时会转为$a=1
</font>
---
总结
```$xslt
in_array()第三个参数决定变量和数组中元素如何进行比较。
值为 false（默认值）时，相当于 ==，值为 true 时，相当于 ===。
```
