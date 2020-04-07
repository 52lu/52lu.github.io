---
title: PHP-字符串函数
date: 2017-09-22 12:12:42
tags:
 - php
categories:
 - 后端编程
---

#### 1.bin2hex - 函数把包含数据的二进制字符串转换为十六进制值
```php
<?php
$data='php is best';
$hex = bin2hex($data);
echo $hex ;
// 输出: 7068702069732062657374
```
#### 2.hex2bin — 转换十六进制字符串为二进制字符串
```php
<?php
$data='7068702069732062657374';
$bin = hex2bin($data);
echo $bin ;
// 输出: php is best
```

#### 3.implode — 将一个一维数组的值转化为字符串
```php
<?php
$data=['PHP','IS','BEST'];
$d= implode(',',$data);
var_dump($d);
// 输出: string(11) "PHP,IS,BEST"
```
#### 4.explode — 使用一个字符串分割另一个字符串

```php
<?php
$data='PHP,IS,BEST';
$d= explode(',',$data);
var_dump($d);


/** 输出:
array(3) {
 [0]=>
 string(3) "PHP"
 [1]=>
 string(2) "IS"
 [2]=>
 string(4) "BEST"
}
*/
```

#### 5.lcfirst — 将字符串的首字母转换为小写
```php
<?php
$data='PHP,IS,BEST';
$d= lcfirst($data);
var_dump($d);
// 输出:string(11) "pHP,IS,BEST"
```

#### 6.ucfirst — 将字符串的首字母转换为大写
```php
<?php
$data='php,is,best';
$d= ucfirst($data);
var_dump($d);
//输出: string(11) "Php,is,best"
```

#### 7.ucwords — 将字符串中每个单词的首字母转换为大写
```php
<?php
$data='php is best';
$d= ucwords($data);
var_dump($d);

// 输出: string(11) "Php Is Best"
```


#### 8.strtoupper — 将字符串转化为大写
```php
<?php
$data='php,is,best';
$d= strtoupper($data);
var_dump($d);

// 输出: string(11) "PHP,IS,BEST"
```

#### 9.strtolower — 将字符串转化为小写
```php
<?php
$data='PHP,IS,BEST';
$d= strtolower($data);
var_dump($d);

// 输出: string(11) "php,is,best"
```

#### 10.strlen — 获取字符串长度
```php
<?php
$data='php is best';
$d= strlen($data);
var_dump($d);

// 输出:int(11)
```

#### 11.strpos — 查找字符串首次出现的位置
```php
<?php
$data='php is best';
$d= strpos($data,'is');
var_dump($d);

// 输出:int(4)
```

#### 12.strripos — 计算指定字符串在目标字符串中最后一次出现的位置(不区分大小写)
```php
<?php
$haystack = 'ababcd';
$needle  = 'aB';
$pos= strripos($haystack, $needle);

if ($pos === false) {
    echo "未发现";
} else {
    echo "找到！";
}
// 输出:"找到！"
```
#### 13.strrpos - 计算指定字符串在目标字符串中最后一次出现的位置
```php
<?php
$haystack = 'ababcd';
$needle  = 'aB';
$pos = strripos($haystack, $needle);

if ($pos === false) {
    echo "未发现";
} else {
    echo "找到！";
}
// 输出:"未发现"
```
#### 14.strstr - 查找字符串的首次出现
**语法:**
```php
strstr ( string $haystack , mixed $needle [, bool $before_needle = FALSE ] ) : string
```
**结果:** 返回 haystack 字符串从 needle 第一次出现的位置开始到 haystack 结尾的字符串。

**注意:**
- 函数区分大小写。如果想要不区分大小写，请使用 stristr()。
- 如果你仅仅想确定 needle 是否存在于 haystack 中，请使用速度更快、耗费内存更少的 strpos() 函数。


**示例:** 
```
<?php
$email  = 'name@example.com';
$domain = strstr($email, '@');
echo $domain; // 打印 @example.com

$user = strstr($email, '@', true); // 从 PHP 5.3.0 起
echo $user; // 打印 name

```

#### 15.str_replace - 字符串替换

**语法:** 
```php
str_replace ( mixed $search , mixed $replace , mixed $subject [, int &$count ] ) : mixed
```
**结果:** 将subject 中全部的 search 都被 replace 替换之后的结果。



**示例:** 
```
<?php
// 单字符串替换

$str='php is best';
$a = str_replace('php', 'Go', $str);
echo $a.PHP_EOL;
// 输出: Go is best

//批量替换
$phrase  = "You should eat fruits, vegetables, and fiber every day.";
$healthy = array("fruits", "vegetables", "fiber");
$yummy   = array("pizza", "beer", "ice cream");

$newphrase = str_replace($healthy, $yummy, $phrase);
echo $newphrase;

// 输出: You should eat pizza, beer, and ice cream every day.

```

#### 16.str_repeat - 重复一个字符串

**语法:** 
```php
str_repeat ( string $input , int $multiplier ) : string
```
**结果:** 返回 input 重复 multiplier 次后的结果。

**示例:** 
```
<?php
echo str_repeat("*", 10);
// 输出:**********
```

#### 17.str_shuffle - 随机打乱一个字符串

**语法:** 
```php
str_shuffle ( string $str ) : string
```
**结果:** 返回打乱后的字符串。

**示例:** 
```
<?php
echo str_shuffle("123456");
// 输出 36251
```

#### 18.str_repeat - 重复一个字符串

**语法:** 
```php
str_repeat ( string $input , int $multiplier ) : string
```
**结果:** 返回 input 重复 multiplier 次后的结果。

**示例:** 
```
<?php
echo str_repeat("*", 10);
// 输出:**********
```

#### 20.ltrim — 删除字符串开头的空白字符（或其他字符）

**语法:** 
```php
ltrim ( string $str [, string $character_mask ] ) : string
```

**示例:** 
```
<?php
$str = ' Abc';
// 删除开头空格
echo ltrim($str).PHP_EOL;

$str2 = '@Abc';
// 删除开头指定字符串
echo ltrim($str2,'@').PHP_EOL;

/* 输出:
Abc
Abc
/
```
#### 21.rtrim — 删除字符串末端的空白字符（或者其他字符）
**语法:** 
```php
rtrim ( string $str [, string $character_mask ] ) : string
```

**示例:** 
```
<?php
$str = 'Abc ';
// 删除末尾空格
echo ltrim($str).PHP_EOL;

$str2 = 'AbcE';
// 删除末尾指定字符串(E)
echo ltrim($str2,'E').PHP_EOL;

/* 输出:
Abc
Abc
/
```

#### 22.trim — 去除字符串首尾处的空白字符（或者其他字符）
**语法:** 
```php
trim ( string $str [, string $character_mask = " \t\n\r\0\x0B" ] ) : string
```
**示例:** 
```
<?php
$str = ' Abc ';
// 删除末尾空格
echo trim($str).PHP_EOL;

$str2 = '@ABC@';
// 删除末尾指定字符串(E)
echo trim($str2,'@').PHP_EOL;

/* 输出:
Abc
ABC
/
```

#### 23.substr_count — 计算字串出现的次数

**语法:** 
```php
substr_count ( string $haystack , string $needle [, int $offset = 0 [, int $length ]] ) : int
```

**结果:** substr_count() 返回子字符串needle 在字符串 haystack 中出现的次数。注意 needle 区分大小写。

**示例:** 
```
<?php
$text = 'This is a test';
echo substr_count($text, 'is').PHP_EOL;
// 输出: 2

```

#### 24.substr — 返回字符串的子串

**语法:** 
```php
substr ( string $string , int $start [, int $length ] ) : string
```

**结果:** 返回字符串 string 由 start 和 length 参数指定的子字符串。

**注意start值:**
- 如果start是非负数，返回的字符串将从string的start位置开始，从0开始计算
- 如果start是负数，返回的字符串将从string结尾处向前数第start个字符开始。
- 如果string的长度小于start将返回 FALSE。



**示例:** 
```
<?php
// $start 使用负数
$rest = substr("abcdef", -1);    // 返回 "f"
$rest = substr("abcdef", -2);    // 返回 "ef"
$rest = substr("abcdef", -3, 1); // 返回 "d"

// $length 使用负数
$rest = substr("abcdef", 0, -1);  // 返回 "abcde"
$rest = substr("abcdef", 2, -1);  // 返回 "cde"
$rest = substr("abcdef", 4, -4);  // 返回 ""
$rest = substr("abcdef", -3, -1); // 返回 "de"

```

#### 25.str_pad — 使用另一个字符串填充字符串为指定长度


**语法:** 
```php
str_pad ( string $input , int $pad_length [, string $pad_string = " " [, int $pad_type = STR_PAD_RIGHT ]] ) : string
```

**结果:** 该函数返回 input 被从左端、右端或者同时两端被填充到制定长度后的结果。如果可选的 pad_string 参数没有被指定，input 将被空格字符填充，否则它将被 pad_string 填充到指定长度。



**示例:** 
```
<?php
$input = "Alien";
echo str_pad($input, 10);                      // 输出 "Alien     "
echo str_pad($input, 10, "-=", STR_PAD_LEFT);  // 输出 "-=-=-Alien"
echo str_pad($input, 10, "_", STR_PAD_BOTH);   // 输出 "__Alien___"
echo str_pad($input,  6, "___");               // 输出 "Alien_"
echo str_pad($input,  3, "*");                 // 输出 "Alien"
```


[查看更多字符串函数](https://www.php.net/manual/zh/book.strings.php)