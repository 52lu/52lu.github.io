---
title: PHP-魔术函数
date: 2017-09-21 18:20:02
tags:
 - php
categories:
 - 后端编程
---


#### 1.__construct()，类的构造函数

**说明:**
php中构造方法是对象创建完成后第一个被对象自动调用的方法。在每个类中都有一个构造方法，如果没有显示地声明它，那么类中都会默认存在一个没有参数且内容为空的构造方法。

**作用:**
通常构造方法被用来执行一些有用的初始化任务，如对成员属性在创建对象时赋予初始值。


#### 2.__destruct()，类的析构函数

**说明**：
析构方法允许在销毁一个类之前执行的一些操作或完成一些功能，比如说关闭文件、释放结果集等

**作用:**
一般来说，析构方法在PHP中并不是很常用，它属类中可选择的一部分，通常用来完成一些在对象销毁前的清理任务。

**注意**：析构函数不能带有任何参数。




#### 3.__call()，在对象中调用一个不可访问方法时调用

**说明:**
该方法有两个参数，第一个参数 $function_name 会自动接收不存在的方法名，第二个 $arguments 则以数组的方式接收不存在方法的多个参数。

**作用:** 为了避免当调用的方法不存在时产生错误，而意外的导致程序中止，可以使用 __call() 方法来避免。该方法在调用的方法不存在时会自动调用，程序仍会继续执行下去。

**示例:**
```php
<?php
class Test
{                             
    function say()
    {  
        echo "Hello, world!<br>"; 
    }      
        
    /**
     * 当调用不存在的方法时，则自动调用了对象中的__call()方法
     */
    function __call($funName, $arguments)
    { 
        // 输出调用不存在的方法名
        echo "你所调用的方法：" . $funName . "(参数：" ; 
        // 输出调用不存在的方法时的参数列表
        print_r($arguments); 
        // 结束换行 
        echo ")不存在！<br>\n";                      
    }                                          
}
$test = new Test();     
// 调用对象中不存在的方法，则自动调用了对象中的__call()方法
$test->run("dog"); 
$test->fly("小鸟", "乌鸦");       
// 调用存在的方法
$test->say(); 

//输出
/*
 你所调用的方法：run(参数：Array ( [0] => dog ) )不存在！
 你所调用的方法：fly(参数：Array ( [0] =>小鸟 [1] => 乌鸦 ) )不存在！
 Hello, world!
*/
```



#### 4.__callStatic()，用静态方式中调用一个不可访问方法时调用

**说明：**
此方法与上面所说的 __call() 功能除了 __callStatic() 是为静态方法准备的之外，其它都是一样的。


**示例:**
```
<?php
class Test
{                             
    function static say()
    {  
        echo "Hello, world!<br>"; 
    }      
        
    /**
     * 当调用不存在的静态方法时，触发此函数
     */
    function __callStatic($funName, $arguments)
    { 
        // 输出调用不存在的方法名
        echo "你所调用的静态函数：" . $funName . "(参数：" ; 
        // 输出调用不存在的方法时的参数列表
        print_r($arguments); 
        // 结束换行 
        echo ")不存在！<br>\n";                      
    }                                          
}
// 不存在的静态方法，则自动调用了对象中的__callStatic()方法
Test::run("dog"); 
Test::fly("小鸟", "乌鸦");       
// 调用存在的静态方法
Test::say(); 

//输出
/*
  你所调用的静态函数：run(参数：Array ( [0] => dog ) )不存在！
  你所调用的静态函数：eat(参数：Array ( [0] => 小鸟 [1] => 乌鸦 ) )不存在！
  Hello, world!
*/
```

#### 5.__get(),获得一个类的成员变量时调用
**说明:** 在 php 面向对象编程中，类的成员属性被设定为 private 后，如果我们试图在外面调用它则会出现“不能访问某个私有属性”的错误。那么为了解决这个问题，我们可以使用魔术方法 __get()。

**作用:** 在程序运行过程中，通过它可以在对象的外部获取私有成员属性的值。

**示例:**
```php
<?php
class Person
{
    // 设置私有属性，外界不可直接访问
    private $name;
    private $age;

    /**
     * 构造方法用来实例化对象属性
     *
     * @param $name
     * @param $age
     */
    function __construct($name, $age)
    {
        $this->name = $name;
        $this->age = $age;
    }

    /**
     * 在直接获取私有属性值时自动调用一次，以属性名作为参数传入并处理
     *
     * @param $propertyName
     * @return string|int
     */
    public function __get($propertyName)
    {   
        if ($propertyName == "age") {
            if ($this->age > 20) {
                return $this->age - 10;
            } else {
                return $this->$propertyName;
            }
        } else {
            return $this->$propertyName;
        }
    }
}
 // 实例化对象
$p = new Person("张美丽", 28);  
// 直接访问私有属性name，自动触发__get()方法
echo "姓名：" . $p->name . "<br>";
// 直接访问私有属性age，自动触发__get()方法
echo "年龄：" . $p->age . "<br>";

/*
输出:
    姓名：张美丽
    年龄：18
*/
```
> 注意: 如果把属性定义成是static的，那么通过__get()访问也会报错。原因是static的成员，是属于类本身的，不因为实例化而改变。

#### 6.__set()，设置一个类的成员变量时调用
**说明:**  给一个私有属性赋值时，此方法会被触发，传递的参数是被设置的属性名和值

**示例:**

```php
<?php
class Test 
{
	private $name;
	/**
     * 当直接给私有属性赋值时，触发此方法。
     *
     * @param $property
     * @param $value
     */
	public function __set($property,$value)
	{
		if ($property == 'name') {
		  $this->name = $value;
		}
	}
	public function getName(){
		return $this->name;
	}
}
$test = new Test();
// 为私有属性赋值
$test->name ='张三';
echo '名称: ' . $test->getName();

/** 输出:
    名称: 张三
*/
```


#### 7.__isset()，当对不可访问属性调用isset()或empty()时调用

**说明:** 
当对不可访问属性调用 isset() 或 empty() 时，__isset() 会被调用

**说明:** 


```php
<?php
/**
 * MagicFunction.php
 *
 * Programmer : Mr.Liu
 * Created: 2020/3/13 18:07
 *
 *
 */

class Person
{
    public $sex;
    private $name;
    private $age;

    public function __construct($name = "", $age = 18, $sex = '男')
    {
        $this->name = $name;
        $this->age = $age;
        $this->sex = $sex;
    }

    /**
     * @param $content
     *
     * @return bool
     */
    public function __isset($content)
    {
        echo "当在类外部使用isset()或empty()函数,判断私有成员{$content}时，会自动调用".PHP_EOL;
        echo isset($this->$content);
    }
}

$person = new Person("小明", 25); // 初始赋值
echo isset($person->sex).PHP_EOL;
echo isset($person->name).PHP_EOL;
echo empty($person->age).PHP_EOL;

/**
输出:
1
当在类外部使用isset()或empty()函数,判断私有成员name时，会自动调用
1
当在类外部使用isset()或empty()函数,判断私有成员age时，会自动调用
11

*/
```


#### 8.其他魔术函数，
```

__unset()，当对不可访问属性调用unset()时被调用。

__sleep()，执行serialize()时，先会调用这个函数

__wakeup()，执行unserialize()时，先会调用这个函数

__toString()，类被当成字符串时的回应方法

__invoke()，调用函数的方式调用一个对象时的回应方法

__set_state()，调用var_export()导出类时，此静态方法会被调用。

__clone()，当对象复制完成时调用

__autoload()，尝试加载未定义的类

__debugInfo()，打印所需调试信息
```
[原文来自: PHP之十六个魔术方法详解](https://segmentfault.com/a/1190000007250604#item-2-6)