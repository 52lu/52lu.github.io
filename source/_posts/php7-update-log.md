---
title: PHP-php7更新记录
date: 2018-10-06 
tags:
 - php
categories:
 - 后端编程
---

## 一.PHP5.6.x 移植PHP7.0.x

### 1、PHP7.0.x 新特性
- #### 1.1 太空船操作符（组合比较符） (<=>)
太空船操作符用于比较两个表达式，当$a小于、等于或大于$b时它分别返回-1、0或1，比较规则延续常规比较规则。<font color='red'>对象不能进行比较</font>。

```php
<?php
// 整数
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1

// 浮点数
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
 
// 字符串
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
?>

```
<!--more-->
- #### 1.2 null合并运算符
由于日常使用中存在大量同时使用三元表达式和 isset()的情况， 我们添加了null合并运算符 (??) 这个语法糖。如果变量存在且值不为NULL， 它就会返回自身的值，否则返回它的第二个操作数。

```php
<?php
# php7以前
$a = isset($_GET['a']) ? $_GET['a'] : 'none';

#PHP 7
$a = isset($_GET['a']) ?? 'none';

```

- #### 1.3 变量类型声明
变量类型声明有两种模式。一种是强制的，和严格的。允许使用下列类型参数int、string、float、bool

**非严格模式**

```php
<?php

function sumOfInts(int ...$ints)
{
    return array_sum($ints);
}
var_dump(sumOfInts(2, '3', 4.1)); // int(9)

```
**严格模式**

```php
<?php
# 严格模式
declare(strict_types=1);

function add(int $x, int $y)
{
    return $x + $y;
}
var_dump(add('2', 3)); 
// Fatal error: Argument 1 passed to add() must be of the type integer

```
>要使用严格模式，<font color='red'>一个declare声明指令必须放在文件的顶部。这意味着严格声明标量是基于文件可配的。</font> 这个指令不仅影响参数的类型声明，也影响到函数的返回值声明


- #### 1.4 返回值类型声明

增加了返回类型声明，类似参数类型。这样更方便的控制函数的返回值.在函数定义的后面加上:类型名即可

```php
<?php
function fun(int $a): array
{
  return $a;
}
fun(3);//Fatal error

```

- #### 1.5 匿名类
php7允许new class {} 创建一个匿名的对象。

```php
<?php
//php7以前
class Logger
{
    public function log($msg)
    {
        echo $msg;
    }
}

$util->setLogger(new Logger());

// php7+
$util->setLogger(new class {
    public function log($msg)
    {
        echo $msg;
    }
});

```
- #### 1.6 Unicode codepoint 转译语法
这接受一个以16进制形式的 Unicode codepoint，并打印出一个双引号或heredoc包围的 UTF-8 编码格式的字符串。 可以接受任何有效的 codepoint，并且开头的 0 是可以省略的

```php
<?php
echo "\u{aa}";// ª
echo "\u{0000aa}";// ª
echo "\u{9999}";// 香
```

- ####  1.7 Closure::call
闭包绑定 简短干练的暂时绑定一个方法到对象上闭包并调用它。

```php
<?php
class A {private $x = 1;}

// PHP 7 之前版本的代码
$getXCB = function() {return $this->x;};
$getX = $getXCB->bindTo(new A, 'A'); // 中间层闭包
echo $getX();

// PHP 7+ 及更高版本的代码
$getX = function() {return $this->x;};
echo $getX->call(new A);
```
- #### 1.8 带过滤的unserialize

提供更安全的方式解包不可靠的数据。它通过白名单的方式来防止潜在的代码注入

```php
<?php
// 将所有的对象都转换为 __PHP_Incomplete_Class 对象
$data = unserialize($foo, ["allowed_classes" => false]);

// 将除 MyClass 和 MyClass2 之外的所有对象都转换为 __PHP_Incomplete_Class 对象
$data = unserialize($foo, ["allowed_classes" => ["MyClass", "MyClass2"]);

// 默认情况下所有的类都是可接受的，等同于省略第二个参数
$data = unserialize($foo, ["allowed_classes" => true]);

```

- #### 1.9 IntlChar类
新增加的 [IntlChar](http://php.net/manual/zh/class.intlchar.php) 类旨在暴露出更多的 ICU 功能。这个类自身定义了许多静态方法用于操作多字符集的 unicode 字符。

```php
<?php
printf('%x', IntlChar::CODEPOINT_MAX);//10ffff
echo IntlChar::charName('@');//COMMERCIAL AT
var_dump(IntlChar::ispunct('!'));//bool(true)
```
`若要使用此类，请先安装Intl扩展`

- #### 1.10 预期
 预期是向后兼用并增强之前的 [assert()](http://php.net/manual/zh/function.assert.php) 的方法。 它使得在生产环境中启用断言为零成本，并且提供当断言失败时抛出特定异常的能力。

```php
<?php
ini_set('assert.exception', 1);

class CustomError extends AssertionError {}

assert(false, new CustomError('Some error message'));

//输出：Fatal error: Uncaught CustomError: Some error message
?>
```

- #### 1.11 命名空间按组导入
从同一个命名空间下导入的类、函数、常量支持按组一次导入

```php
<?php
//php7以前
use app\model\A;
use app\model\B;

//php7+
use app\model{A,B}
```

- #### 1.12 生成器支持返回表达式
此特性基于 PHP 5.5 版本中引入的生成器特性构建的。 它允许在生成器函数中通过使用 return 语法来返回一个表达式 （但是不允许返回引用值）， 可以通过调用 Generator::getReturn() 方法来获取生成器的返回值， 但是这个方法只能在生成器完成产生工作以后调用一次。

```php
<?php

$gen = (function() {
    yield 1;
    yield 2;

    return 3;
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL;
}

echo $gen->getReturn(), PHP_EOL;

//输出
/**
  1
  2
  3
*/
```
- #### 1.13 生成器委派
现在，只需在最外层生成其中使用 yield from， 就可以把一个生成器自动委派给其他的生成器， Traversable 对象或者 array。

```php
<?php

function gen()
{
    yield 1;
    yield 2;

    yield from gen2();
}

function gen2()
{
    yield 3;
    yield 4;
}

foreach (gen() as $val)
{
    echo $val, PHP_EOL;
}

//输出
// 1
// 2
// 3
// 4
?>
```

- #### 1.14 整数除法函数intdiv

```php
<?php
var_dump(intdiv(10,3)); //3
```

- #### 1.15 会话选项设置
session_start() 可以加入一个数组覆盖php.ini的配置

```php
<?php
session_start([
    'cache_limiter' => 'private',
    'read_and_close' => true,
]);

```
- #### 1.16 preg_replace_callback_array
 在 PHP7 之前，当使用 [preg_replace_callback()](http://php.net/manual/zh/function.preg-replace-callback.php) 函数的时候， 由于针对每个正则表达式都要执行回调函数，可能导致过多的分支代码。 而使用新加的 [preg_replace_callback_array()](http://php.net/manual/zh/function.preg-replace-callback-array.php) 函数， 可以使得代码更加简洁。

```php
<?php
//string preg_replace_callback_array(array $regexesAndCallbacks, string $input);
$tokenStream = []; // [tokenName, lexeme] pairs

$input = <<<'end'
$a = 3; // variable initialisation
end;

// Pre PHP 7 code
preg_replace_callback(
    [
        '~\$[a-z_][a-z\d_]*~i',
        '~=~',
        '~[\d]+~',
        '~;~',
        '~//.*~'
    ],
    function ($match) use (&$tokenStream) {
        if (strpos($match[0], '$') === 0) {
            $tokenStream[] = ['T_VARIABLE', $match[0]];
        } elseif (strpos($match[0], '=') === 0) {
            $tokenStream[] = ['T_ASSIGN', $match[0]];
        } elseif (ctype_digit($match[0])) {
            $tokenStream[] = ['T_NUM', $match[0]];
        } elseif (strpos($match[0], ';') === 0) {
            $tokenStream[] = ['T_TERMINATE_STMT', $match[0]];
        } elseif (strpos($match[0], '//') === 0) {
            $tokenStream[] = ['T_COMMENT', $match[0]];
        }
    },
    $input
);

// PHP 7+ code
preg_replace_callback_array(
    [
        '~\$[a-z_][a-z\d_]*~i' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_VARIABLE', $match[0]];
        },
        '~=~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_ASSIGN', $match[0]];
        },
        '~[\d]+~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_NUM', $match[0]];
        },
        '~;~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_TERMINATE_STMT', $match[0]];
        },
        '~//.*~' => function ($match) use (&$tokenStream) {
            $tokenStream[] = ['T_COMMENT', $match[0]];
        }
    ],
    $input
);
```
- #### 1.17 随机数、随机字符函数
新加入两个跨平台的函数： [random_bytes()](http://php.net/manual/zh/function.random-bytes.php) 和 [random_int()](http://php.net/manual/zh/function.random-int.php) 用来产生高安全级别的随机字符串和随机整数。

```php
<?php
$bytes = random_bytes(5);
var_dump(bin2hex($bytes));//string(10) "385e33f741"

var_dump(random_int(100, 999));//int(248)
var_dump(random_int(-1000, 0));//int(-898)

```
- #### 1.18 define 支持定义数组
```php
<?php
define('ALLOWED_IMAGE_EXTENSIONS', ['jpg', 'jpeg', 'gif', 'png']);
```
[查看所有新特性详情](http://php.net/manual/zh/migration70.new-features.php)

### 2、PHP7.0.x 新变化
- #### 2.1 错误和异常处理相关的变更
在PHP7 中，很多致命错误以及可恢复的致命错误，都被转换为异常来处理了。 这些异常继承自 Error 类，此类实现了 Throwable 接口 （所有异常都实现了这个基础接口）。


`这也意味着，当发生错误的时候，以前代码中的一些错误处理的代码将无法被触发。 因为在 PHP7 版本中，已经使用抛出异常的错误处理机制了。（如果代码中没有捕获 Error 异常，那么会引发致命错误）`

**set_exception_handler() 不再保证收到的一定是 Exception 对象**

_抛出Error对象时，如果set_exception_handler()里的异常处理代码声明了类型 Exception ，将会导致fatal error。
想要异常处理器同时支持PHP5和PHP7，应该删掉异常处理器里的类型声明。如果代码仅仅是升级到PHP7，则可以把类型 Exception替换成Throwable_
```php
<?php
// PHP 5 时代的代码将会出现问题
function handler(Exception $e) { ... }
set_exception_handler('handler');

// 兼容 PHP 5 和 7
function handler($e) { ... }

// 仅支持 PHP 7
function handler(Throwable $e) { ... }
?>
```



- #### 2.2 list() 会按照原来的顺序进行赋值。不再是逆序了

```php
list($a,$b,$c) = [1,2,3];
//PHP5
var_dump($a);//3
var_dump($b);//2
var_dump($c);//1


//PHP7+
var_dump($a);//1
var_dump($b);//2
var_dump($c);//3

```
**空的list()赋值支持已经被移除**
list() 结构现在不再能是空的。如下的例子不再被允许：
```php
<?php
list() = $a;
list(,,) = $a;
list($x, list(), $y) = $a;
```
- #### 2.3 foreach不再改变内部数组指针
 在PHP7之前，当数组通过 foreach 迭代时，数组指针会移动。现在开始，不再如此，见下面代码

```php
<?php
$array = [0, 1, 2];
foreach ($array as &$val) {
    var_dump(current($array));
}

//PHP5 输出
int(1);
int(2);
bool(false);

//PHP7+ 输出
int(0);
int(0);
int(0);
```

- #### 2.4 十六进制字符串不再被认为是数字
> 含十六进制字符串不再被认为是数字

```php
<?php
var_dump(is_numeric("0x123"));

//PHP5 输出
bool(true);

//PHP7+ 输出
bool(false);

```
[查看所有变化详情](http://php.net/manual/zh/migration70.incompatible.php)


### 3、PHP7.0.x 废弃的特性

- #### 3.1 PHP4风格的构造函数（方法名和类名一样）将被弃用，并在将来移除。

PHP4 风格的构造函数（方法名和类名一样）将被弃用，并在将来移除。 如果在类中仅使用了 PHP4 风格的构造函数，PHP7 会产生 E_DEPRECATED 警告。 如果还定义了 __construct() 方法则不受影响。

```php
<?php
class foo {
    function foo() {
        echo 'I am the constructor';
    }
}

```
以上例程会输出：
` Deprecated: Methods with the same name as their class will not be constructors in a future version of PHP; foo has a deprecated constructor in example.php on line 3`


- #### 3.2 静态调用非静态的方法
废弃了 静态（Static） 调用未声明成 static 的方法，未来可能会彻底移除该功能。

```php
<?php
class foo {
    function bar() {
        echo 'I am not static!';
    }
}

foo::bar();
```
以上例程会输出：
` Deprecated: Non-static method foo::bar() should not be called statically in - on line 8 I am not static!`

[查看更多PHP7.0.x 弃用的功能](http://php.net/manual/zh/migration70.deprecated.php)

### 4、PHP7.0.x 移除的扩展和 SAPI

- #### 4.1 移除的扩展
  - ereg
  - mssql
  - mysql
  - sybase_ct

- #### 4.2 移除的 SAPI
  - aolserver
  - apache
  - apache_hooks
  - apache2filter
  - caudium
  - continuity
  - isapi
  - milter
  - nsapi
  - phttpd
  - pi3web
  - roxen
  - thttpd
  - tux
  - webjames


## 二.PHP7.0.x 移植PHP7.1.x

 ### 1、PHP7.1.x 新特性
- #### 1.1 可为空(Nullables)类型
参数以及返回值的类型现在可以通过在类型前加上一个问号使之允许为空。 当启用这个特性时，传入的参数或者函数返回的结果要么是给定的类型，要么是null 。

```php
<?php
function test(?string $name)
{
    var_dump($name);
}

test('elePHPant');
test(null);
test();
```
输出结果
```
string(10) "elePHPant"

NULL

Uncaught Error: Too few arguments to function test(), 0 passed in...

```

- #### 1.2 Void 函数
一个新的返回值类型void被引入。 返回值声明为 void 类型的方法要么干脆省去 return 语句，要么使用一个空的 return 语句。 对于 void 函数来说，NULL 不是一个合法的返回值

```php
<?php
function swap(&$left, &$right) : void
{
    if ($left === $right) {
        return;
    }

    $tmp = $left;
    $left = $right;
    $right = $tmp;
}

$a = 1;
$b = 2;
var_dump(swap($a, $b), $a, $b);

```
输出结果
``` 
null
int(2)
int(1)
```
试图去获取一个 void 方法的返回值会得到 NULL ，并且不会产生任何警告。这么做的原因是不想影响更高层次的方法。

- #### 1.3 类常量可见性
 现在起支持设置类常量的可见性(public、protected、private)。

```php
<?php
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```
- #### 1.4 短数组语法（[]）
短数组语法（[]）现在作为list()语法的一个备选项，可以用于将数组的值赋给一些变量（包括在foreach中）。

```php
<?php
$data = [
    [1, 'Tom'],
    [2, 'Fred'],
];

// list() style
list($id1, $name1) = $data[0];

// [] style
[$id1, $name1] = $data[0];

// list() style
foreach ($data as list($id, $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as [$id, $name]) {
    // logic here with $id and $name
}
```
- #### 1.5 iterable伪类
现在引入了一个新的被称为iterable的伪类 (与[callable](http://php.net/manual/zh/language.types.callable.php)类似)。 这可以被用在参数或者返回值类型中，它代表接受数组或者实现了Traversable接口的对象。 至于子类，当用作参数时，子类可以收紧父类的iterable类型到array 或一个实现了Traversable的对象。对于返回值，子类可以拓宽父类的 array或对象返回值类型到iterable。

```php
<?php
function iterator(iterable $iter)
{
    foreach ($iter as $val) {
        //
    }
}
```

- #### 1.6 多异常捕获处理
 一个catch语句块现在可以通过管道字符(|)来实现多个异常的捕获。 这对于需要同时处理来自不同类的不同异常时很有用。

```php
<?php
try {
    // some code
} catch (FirstException | SecondException $e) {
    // handle first and second exceptions
}

```
- #### 1.7 list支持键名
 现在list()和它的新的[]语法支持在它内部去指定键名。这意味着它可以将任意类型的数组 都赋值给一些变量（与短数组语法类似）

```php
<?php
$data = [
    ["id" => 1, "name" => 'Tom'],
    ["id" => 2, "name" => 'Fred'],
];

// list() style
list("id" => $id1, "name" => $name1) = $data[0];

// [] style
["id" => $id1, "name" => $name1] = $data[0];

// list() style
foreach ($data as list("id" => $id, "name" => $name)) {
    // logic here with $id and $name
}

// [] style
foreach ($data as ["id" => $id, "name" => $name]) {
    // logic here with $id and $name
}
```

- #### 1.8 支持为负的字符串偏移量
现在所有支持偏移量的[字符串操作函数](http://php.net/manual/zh/book.strings.php) 都支持接受负数作为偏移量，包括通过[]或{}操作字符串下标。在这种情况下，一个负数的偏移量会被理解为一个从字符串结尾开始的偏移量。

```php
<?php
var_dump("abcdef"[-2]);//string (1) "e"
var_dump(strpos("aabbcc", "b", -3));//int(3)

$string = 'bar';
echo "The last character of '$string' is '$string[-1]'.\n";

//输出: The last character of 'bar' is 'r'.
```

- #### 1.9 将callables转为闭包
[Closure](http://php.net/manual/zh/class.closure.php)新增了一个静态方法fromCallable()，用于将callable快速地 转为一个Closure 对象。

```php
<?php
class Test
{
    public function exposeFunction()
    {
        return Closure::fromCallable([$this, 'privateFunction']);
    }

    private function privateFunction($param)
    {
        var_dump($param);
    }
}

$privFunc = (new Test)->exposeFunction();
$privFunc('some value');

//输出:string(10) "some value"

```

- #### 1.10 http2服务推送
>对服务器推送的支持现在已经被加入到CURL扩展中（ 需要版本 7.46 或更高）。这个可以通过[curl_multi_setopt()](http://php.net/manual/zh/function.curl-multi-setopt.php)函数与新的常量 **CURLMOPT_PUSHFUNCTION** 来进行调节。常量 **CURL_PUST_OK** 和 **CURL_PUSH_DENY** 也已经被添加进来，以便服务器推送的回调函数来表明自己会同意或拒绝处理。

### 2、PHP7.1.x 新变化
- #### 2.1 传递参数过少时将抛出错误
>过去我们传递参数过少会产生warning。php7.1开始会抛出error


### 3、PHP 7.1.x 中废弃的特性
- #### 3.1 移除了ext/mcrypt拓展
> mcrypt 扩展已经过时了大约10年，并且用起来很复杂。因此它被废弃并且被 OpenSSL 所取代。 从PHP7.2起它将被从核心代码中移除并且移到PECL中。


## 三.PHP7.1.x 移植PHP7.2.x

 ### 1、PHP7.2.x 新特性
 
- #### 1.1 增加新的类型object
 `这种新的对象类型, object, 引进了可用于逆变（contravariant）参数输入和协变（covariant）返回任何对象类型。`
 
```php
<?php

function test(object $obj) : object
{
    return new SplQueue();
}
test(new StdClass());
```
- #### 1.2 通过名称加载扩展
<font size="2">扩展文件不再需要通过文件加载 (Unix下以.so为文件扩展名，在Windows下以 .dll 为文件扩展名) 进行指定。可以在php.ini配置文件进行启用, 也可以使用 [dl()](http://php.net/manual/zh/function.dl.php) 函数进行启用。</font>
``` 
; ini file
extension=php-ast
zend_extension=opcache
```

- #### 1.3 允许重写抽象方法
`当一个抽象类继承于另外一个抽象类的时候，继承后的抽象类可以重写被继承的抽象类的抽象方法`

```php
<?php
abstract class A
{
    abstract function test(string $s);
}
abstract class B extends A
{
    // overridden - still maintaining contravariance for parameters and covariance for return
    abstract function test($s) : int;
}
```
- #### 1.4 使用Argon2算法生成密码散列
<font size="2">Argon2 已经被加入到密码散列（password hashing） API ([这些函数以 password_ 开头](http://php.net/manual/zh/book.password.php)) 以下是暴露出来的常量:`</front>
- PASSWORD_ARGON2I
- PASSWORD_ARGON2_DEFAULT_MEMORY_COST
- PASSWORD_ARGON2_DEFAULT_TIME_COST
- PASSWORD_ARGON2_DEFAULT_THREADS
- #### 1.5 新增 ext/PDO（PDO扩展） 字符串扩展类型 ¶
`当你准备支持多语言字符集，PDO的字符串类型已经扩展支持国际化的字符集。以下是扩展的常量：`

- PDO::PARAM_STR_NATL
- PDO::PARAM_STR_CHAR
- PDO::ATTR_DEFAULT_STR_PARAM

这些常量通过PDO::PARAM_STR利用位运算OR进行计算：

```php
<?php
$db->quote('über', PDO::PARAM_STR | PDO::PARAM_STR_NATL);
```
- #### 1.5 允许分组命名空间的尾部逗号
<font size="2">命名空间可以在PHP7.2中使用尾随逗号进行分组引入。</font>

```php
<?php

use Foo\Bar\{
    Foo,
    Bar,
    Baz,
};
```
> <front color="red">在php7.0中 最后一个(Baz,)不能有逗号否则报错</front>

### 2、PHP7.2.x 新变化
- #### 2.1 number_format 返回值

```php
<?php
var_dump(number_format(-0.01)); // now outputs string(1) "0" instead of string(2) "-0"
```
- #### 2.2 get_class()不再允许null。

```php
<?php
var_dump(get_class(null)); // warning
```
- #### 2.3 count 作用在不是 Countable Types 将发生warning

```php
<?php
//PHP7.2
var_dump(
    count(null), // NULL is not countable
    count(1), // integers are not countable
    count('abc'), // strings are not countable
    count(new stdclass), // objects not implementing the Countable interface are not countable
    count([1,2]) // arrays are countable
);
```
以上例程会输出:
```
Warning: count(): Parameter must be an array or an object that implements Countable in %s on line %d

Warning: count(): Parameter must be an array or an object that implements Countable in %s on line %d

Warning: count(): Parameter must be an array or an object that implements Countable in %s on line %d

Warning: count(): Parameter must be an array or an object that implements Countable in %s on line %d
int(0)
int(1)
int(1)
int(1)
int(2)
```
- #### 2.4 不带引号的字符串
`在之前不带引号的字符串是不存在的全局常量，转化成他们自身的字符串。现在将会产生waring。`

```php
<?php

var_dump(HELLO);

// PHP7.2前输出：HELLO

/*
 * PHP7.2输出
Warning: Use of undefined constant HELLO - assumed 'HELLO' (this will throw an Error in a future version of PHP) in /usr/src/php/dfe807d9bfa587a80b60fbdda823e7be.php on line 2
string(5) "HELLO"
*/
```
- #### 2.5  is_object、gettype修正

is_object 作用在**__PHP_Incomplete_Class** 将返回：true
`Previously, using is_object() on the __PHP_Incomplete_Class class would return FALSE. Now, TRUE will be returned.`

gettype作用在闭包在将正确返回resource
`Previously, using gettype() on a closed resource would return a string of "unknown type". Now, a string of "resource (closed)" will be returned.`

### 3、PHP7.2.x 废弃的特性
- #### 3.1 __autoload 被废弃
- #### 3.2 each被废弃
`使用此函数遍历时，比普通的 foreach 更慢， 并且给新语法的变化带来实现问题。因此它被废弃了`

- #### 3.3 gmp_random()函数被废弃
此函数基于未知的、取决于平台的 limb 尺寸产生随机数。因此，该函数已被废弃。 使用更好的方式产生随机数： GMP 扩展中的[gmp_random_bits()](http://php.net/manual/zh/function.gmp-random-bits.php)  和 [gmp_random_range()](http://php.net/manual/zh/function.gmp-random-range.php)。
