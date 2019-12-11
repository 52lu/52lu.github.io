---
title: PHP-数组函数
date: 2017-09-20 13:12
tags:
 - php
 - 面试题
categories:
 - 后端编程
---

# 1.改变
## 1.1 [array_chunk 分割](https://www.php.net/manual/zh/function.array-chunk.php)

**功能:** 将一个数组分割成多个
**语法:**
```php
array_chunk( array $array , int $size [, bool $preserve_keys = false ] ) : array
```
**说明:** 将一个数组分割成多个数组，其中每个数组的单元数目由 size 决定。最后一个数组的单元数目可能会少于 size 个。
**代码:**

```php
<?php
$input_array = array("a"=>"apple","b"=>"blue","c","d","e");

print_r(array_chunk($input_array, 2));
//设为 TRUE，可以保留数组中原来的键名
print_r(array_chunk($input_array, 2,True));
```
**输出:**

```php
Array
(
 [0] => Array
  (
   [0] => apple
   [1] => blue
  )
 [1] => Array
  (
   [0] => c
   [1] => d
  )
 [2] => Array
  (
   [0] => e
  )
)

Array
(
 [0] => Array
  (
   [a] => apple
   [b] => blue
  )
 [1] => Array
  (
   [0] => c
   [1] => d
  )
 [2] => Array
  (
   [2] => e
  )
)
```

## 1.2 [array_merge 合并](https://www.php.net/manual/zh/function.array-merge.php)
**功能:** 合并一个或多个数组
**说明:** 如果后面有键名相同的会覆盖掉前面的内容，键名为数字的会添加到后面
**语法:**
```php
array_merge ( array $array1 [, array $... ] ) : array
```
**代码:**
```php
<?php
$array1 = array("color"=>"red",2,4);
$array2 = array("a","b","color"=>"green","shape"=>"trapezoid",4);
$result = array_merge($array1,$array2);
print_r($result);
```
**输出:**
```php
Array
(
 [color] => green
 [0] => 2
 [1] => 4
 [2] => a
 [3] => b
 [shape] => trapezoid
 [4] => 4
)
```



## 1.3 [array_unique 去重](https://www.php.net/manual/zh/function.array-unique.php)
**功能:** 移除数组中重复的值
**说明:** 接受 array 作为输入并返回没有重复值的新数组。注意键名保留不变。array_unique() 先将值作为字符串排序，然后对每个值只保留第一个遇到的键名，接着忽略所有后面的键名。这并不意味着在未排序的 array 中同一个值的第一个出现的键名会被保留。
**语法:**
```php
array_unique ( array $array [, int $sort_flags = SORT_STRING ] ) : array
```

**代码1:**

```php
<?php
$input = array("a" => "green", "red", "b" => "green", "blue", "red");
$result = array_unique($input);
print_r($result);
?>
```

**输出:**
```php
Array
(
    [a] => green
    [0] => red
    [1] => blue
)

```


**代码2:**

```php
<?php
$input = array(4, "4", "3", 4, 3, "3");
$result = array_unique($input);
var_dump($result);
?>
```

**输出:**
```php
array(2) {
  [0] => int(4)
  [2] => string(1) "3"
}
```




**输出:**
```php
Array
(
   [0] => Tank
   [1] => Morpheus
   [2] => Cypher
   [3] => Trinity
   [4] => Neo
)

```

## 1.4 [array_combine 创建](https://www.php.net/manual/zh/function.array-combine.php)
**功能:**  创建一个数组，用一个数组的值作为其键名，另一个数组的值作为其值
**语法:**
```php
array_combine ( array $keys , array $values ) : array
```
**说明:** 返回一个 array，用来自 keys 数组的值作为键名，来自 values 数组的值作为相应的值。

**代码:**

```php
<?php
$a = array("one","two","three");
$b = array("一","二","三");
$c = array_combine($a,$b);
print_r($c);
```

**输出:**
```php
( [one] => 一 [two] => 二 [three] => 三 )
```

## 1.5 [array_fill 创建](https://www.php.net/manual/zh/function.array-fill.php)
**功能:** 用给定的值填充数组
**语法:**
```php
array_fill ( int $start_index , int $num , mixed $value ) : array
```
**说明:** 用value参数的值将一个数组填充num个条目，键名由start_index 参数指定的开始。

**代码:**

```php
<?php
$a = array_fill(5, 6, 'banana');
$b = array_fill(-2, 4, 'pear');
print_r($a);
print_r($b);
?>
```

**输出:**
```php
Array
(
    [5]  => banana
    [6]  => banana
    [7]  => banana
    [8]  => banana
    [9]  => banana
    [10] => banana
)
Array
(
    [-2] => pear
    [0] => pear
    [1] => pear
    [2] => pear
)
```



## 1.6 [array_fill_keys 创建](https://www.php.net/manual/zh/function.array-fill-keys.php)
**功能:** 使用指定的键和值填充数组
**语法:**
```php
array_fill_keys ( array $keys , mixed $value ) : array
```
**说明:** 使用value参数的值作为值，使用 keys 数组的值作为键来填充一个数组。

**代码:**

```php
<?php
$keys = array('foo', 5, 10, 'bar');
$a = array_fill_keys($keys, 'banana');
print_r($a);
?>
?>
```

**输出:**
```php
Array
(
    [foo] => banana
    [5] => banana
    [10] => banana
    [bar] => banana
)
```


## 1.7 [array_flip 交换](https://www.php.net/manual/zh/function.array-flip.php)
**功能:** 交换数组中的键和值
**语法:**
```php
array_flip ( array $array ) : array
```
**说明:** 返回一个反转后的array，例如 array 中的键名变成了值，而 array 中的值成了键名。

**注意:**
- array中的值需要能够作为合法的键名(integer或者string）。如果类型不对，将出现一个警告，并且有问题的键／值对将不会出现在结果里。
- 如果同一个值出现多次，则最后一个键名将作为它的值，其它键会被丢弃。

**代码:**
```php
<?php
$input = array("oranges", "apples", "pears");
$flipped = array_flip($input);
print_r($flipped);

$input2 = array("a" => 1, "b" => 1, "c" => 2);
$flipped2 = array_flip($input2);
print_r($flipped2);
?>
```

**输出:**
```php
Array
(
    [oranges] => 0
    [apples] => 1
    [pears] => 2
)

Array
(
    [1] => b
    [2] => c
)
```

## 1.8 [array_pad 新增](https://www.php.net/manual/zh/function.array-pad.php)
**功能:** 以指定长度将一个值填充进数组
**语法:**
```php
array_pad(array $array , int $size , mixed $value ) : array
```
**说明:**  返回array的一个拷贝，并用value将其填补到 size 指定的长度。如果 size 为正，则填补到数组的右侧，如果为负则从左侧开始填补。如果 size 的绝对值小于或等于 array 数组的长度则没有任何填补。

**代码:**
```php
<?php
$input = array(12, 10, 9);

$result = array_pad($input, 5, 0);
// result is array(12, 10, 9, 0, 0)

$result = array_pad($input, -7, -1);
// result is array(-1, -1, -1, -1, 12, 10, 9)

$result = array_pad($input, 2, "noop");
//  $input 数组不变
?>
```

## 1.9 [array_push 新增](https://www.php.net/manual/zh/function.array-push.php)
**功能:** 将一个或多个单元压入数组的末尾（入栈）
**语法:**
```php
array_push(array &$array, mixed $value1 [, mixed $... ]) : int
```
**说明:** 将array当成一个栈，并将传入的变量压入array的末尾。array的长度将根据入栈变量的数目增加。

**注意:**
- 如果用array_push()来给数组增加一个单元，还不如用$array[]= ，因为这样没有调用函数的额外负担。

**代码:**
```php
<?php
$stack = array("orange", "banana");
array_push($stack, "apple", "raspberry");
print_r($stack);
?>
```

**输出:**
```php
Array
(
    [0] => orange
    [1] => banana
    [2] => apple
    [3] => raspberry
)
```
## 1.10 [array_pop 删除](https://www.php.net/manual/zh/function.array-pop.php)
**功能:** 弹出数组最后一个单元（出栈）
**语法:**
```php
array_pop ( array &$array ) : mixed
```
**说明:** 弹出并返回array数组的最后一个单元，并将数组array的长度减一。


**代码:**
```php
<?php
$stack = array("orange", "banana", "apple", "raspberry");
$fruit = array_pop($stack);
print_r($stack);
print_r($fruit);
?>
```

**输出:**
```php
Array
(
   [0] => orange
   [1] => banana
   [2] => apple
)
raspberry
```

## 1.11 [shuffle 打乱](https://www.php.net/manual/zh/function.shuffle.php)
**功能:** 打乱数组
**说明:** 本函数打乱（随机排列单元的顺序）一个数组。 它使用的是伪随机数产生器，并不适合密码学的场合
**语法:**
```php
shuffle ( array &$array ) : bool
```

**代码:**

```php
<?php
$input = array("Neo", "Morpheus", "Trinity", "Cypher", "Tank");
shuffle($input);
print_r($input);
?>
```

## 1.12 [array_replace 替换](https://www.php.net/manual/zh/function.array-replace.php)
**功能:** 使用传递的数组替换第一个数组的元素
**语法:**
```php
array_replace ( array $array1 [, array $... ] ) : array
```
**说明:** 使用后面数组元素相同key的值替换array1数组的值。
- 如果一个键存在于第一个数组同时也存在于第二个数组，它的值将被第二个数组中的值替换。
- 如果一个键存在于第二个数组，但是不存在于第一个数组，则会在第一个数组中创建这个元素。
- 如果一个键仅存在于第一个数组，它将保持不变。
- 如果传递了多个替换数组，它们将被按顺序依次处理，后面的数组将覆盖之前的值

**代码:**
```php
<?php
$base = array("orange", "banana", "apple", "raspberry");
$replacements = array(0 => "pineapple", 4 => "cherry");
$replacements2 = array(0 => "grape");

$basket = array_replace($base, $replacements, $replacements2);
print_r($basket);
?>
```

**输出:**
```php
Array
(
    [0] => grape
    [1] => banana
    [2] => apple
    [3] => raspberry
    [4] => cherry
)
```

## 1.13 [array_shift 删除](https://www.php.net/manual/zh/function.array-shift.php)
**功能:** 将数组开头的单元移出数组
**语法:**
```php
array_shift ( array &$array ) : mixed
```
**说明:** 将 array 的第一个单元移出并作为结果返回，将 array 的长度减一并将所有其它单元向前移动一位。所有的数字键名将改为从零开始计数，文字键名将不变。

**代码:**
```php
<?php
$stack = array("orange", "banana", "apple", "raspberry");
$fruit = array_shift($stack);
print_r($stack);
print_r($fruit);
?>
```

**输出:**
```php
Array
(
   [0] => banana
   [1] => apple
   [2] => raspberry
)
orange
```


## 1.14 [array_unshift 新增](https://www.php.net/manual/zh/function.array-unshift.php)
**功能:** 在数组开头插入一个或多个单元
**语法:**
```php
array_unshift ( array &$array [, mixed $... ] ) : int
```
**说明:** 将传入的单元插入到 array 数组的开头。注意单元是作为整体被插入的，因此传入单元将保持同样的顺序。所有的数值键名将修改为从零开始重新计数，所有的文字键名保持不变。

**代码:**
```php
<?php
$queue = array("orange", "banana");
array_unshift($queue, "apple", "raspberry");
print_r($queue);
?>
```

**输出:**
```php
Array
(
    [0] => apple
    [1] => raspberry
    [2] => orange
    [3] => banana
)
```


## 1.15  [array_splice 删除&替换](https://www.php.net/manual/zh/function.array-splice.php)
**功能:** 去掉数组中的某一部分并用其它值取代
**语法:**
```php
array_splice(array &$input,int $offset [,int$length=count($input) [,mixed$replacement=array()]]) :array
```
**说明:** 把input数组中由offset和length指定的单元去掉，如果提供了replacement参数，则用其中的单元取代。
- 如果offset为正，则从input数组中该值指定的偏移量开始移除。
- 如果offset为负，则从input末尾倒数该值指定的偏移量开始移除。
- 如果省略length，则移除数组中从offset到结尾的所有部分。
- 如果指定了length并且为正值，则移除这么多单元。
- 如果指定了length并且为负值，则移除从offset到数组末尾倒数length为止中间所有的单元。
- 如果设置了length为零，不会移除单元。
- 如果给出了replacement数组，则被移除的单元被此数组中的单元替代。

**注意:**  input中的数字键名不被保留。
**代码:**

```php
<?php
$input = array("red", "green", "blue", "yellow");
array_splice($input, 2);
// $input is now array("red", "green")

$input = array("red", "green", "blue", "yellow");
array_splice($input, 1, -1);
// $input is now array("red", "yellow")

$input = array("red", "green", "blue", "yellow");
array_splice($input, 1, count($input), "orange");
// $input is now array("red", "orange")

$input = array("red", "green", "blue", "yellow");
array_splice($input, -1, 1, array("black", "maroon"));
// $input is now array("red", "green",
//          "blue", "black", "maroon")

$input = array("red", "green", "blue", "yellow");
array_splice($input, 3, 0, "purple");
// $input is now array("red", "green",
//          "blue", "purple", "yellow");
?>
```





# 2.访问
## 2.1 [array_column 指定列](https://www.php.net/manual/zh/function.array-column.php)
**功能:** 返回数组中指定的一列
**语法:**
```php
array_column ( array $input , mixed $column_key [, mixed $index_key = null ] ) : array
```
**说明:** 返回input数组中键值为column_key的列，如果指定了可选参数index_key，那么input数组中的这一列的值将作为返回数组中对应值的键。

**代码:**
- 不传$index_key时

```php
<?php
$records = array(
    array(
        'id' => 2135,
        'first_name' => 'John',
        'last_name' => 'Doe',
    ),
    array(
        'id' => 3245,
        'first_name' => 'Sally',
        'last_name' => 'Smith',
    ),
    array(
        'id' => 5342,
        'first_name' => 'Jane',
        'last_name' => 'Jones',
    ),
    array(
        'id' => 5623,
        'first_name' => 'Peter',
        'last_name' => 'Doe',
    )
);
 
$first_names = array_column($records, 'first_name');
print_r($first_names);
?>
```

**输出:**
```php
Array
(
    [0] => John
    [1] => Sally
    [2] => Jane
    [3] => Peter
)
```

- 传$index_key时

```php
$last_names = array_column($records, 'last_name', 'id');
print_r($last_names);
```
**输出:**
```php
Array
(
    [2135] => Doe
    [3245] => Smith
    [5342] => Jones
    [5623] => Doe
)
```


## 2.2 [array_keys 所有key](https://www.php.net/manual/zh/function.array-keys.php)
**功能:** 返回数组中部分的或所有的键名
**语法:**
```php
array_keys ( array $array [, mixed $search_value = null [, bool $strict = false ]] ) : array
```
**说明:** 返回input数组中的数字或者字符串的键名。
- 如果指定了可选参数 search_value，则只返回该值的键名。否则 input 数组中的所有键名都会被返回。

**代码:**

```php
<?php
// 所有key
$array = array(0 => 100, "color" => "red");
print_r(array_keys($array));

// 指定value的key
$array = array("blue", "red", "green", "blue", "blue");
print_r(array_keys($array, "blue"));

$array = array("color" => array("blue", "red", "green"),
               "size"  => array("small", "medium", "large"));
print_r(array_keys($array));
?>
```

**输出:**
```php
Array
(
    [0] => 0
    [1] => color
)
Array
(
    [0] => 0
    [1] => 3
    [2] => 4
)
Array
(
    [0] => color
    [1] => size
)
```



## 2.3 [array_key_first 首个key](https://www.php.net/manual/zh/function.array-key-first.php)
**功能:** 返回数组中的第一个key
**注意:** 需要PHP版本:PHP7.3+
**语法:**
```php
array_key_first ( array $array ) : mixed
```

**代码:**

```php
<?php
$array = ['a' => 1, 'b' => 2, 'c' => 3];
$firstKey = array_key_first($array);
var_dump($firstKey);
?>
```

**输出:**
```php
string(1) "a"
```

## 2.4 [array_key_last 末尾key](https://www.php.net/manual/zh/function.array-key-last.php)
**功能:** 返回数组中的第一个key
**注意:** 需要PHP版本:PHP7.3+
**语法:**
```php
array_key_last ( array $array ) : mixed
```

**代码:**

```php
<?php
$array = ['a' => 1, 'b' => 2, 'c' => 3];
$firstKey = array_key_last($array);
var_dump($firstKey);
?>
```

**输出:**
```php
string(1) "c"
```

## 2.5 [array_rand 随机取出](https://www.php.net/manual/zh/function.array-rand.php)
**功能:** 从数组中随机取出一个或多个单元
**说明:** 从数组中取出一个或多个随机的单元，并返回随机条目的一个或多个键。 它使用了伪随机数产生算法，所以不适合密码学场景
**语法:**
```php
array_rand ( array $array [, int $num = 1 ] ) : mixed
```

**代码:**

```php
<?php
$input = array("Neo", "Morpheus", "Trinity", "Cypher", "Tank");
$rand_keys = array_rand($input, 2);
print_r($rand_keys);
?>
```

**输出:**
```php
Array
(
   [0] => 0
   [1] => 3
)
```


## 2.6 [array_reverse 反转](https://www.php.net/manual/zh/function.array-reverse.php)
**功能:** 返回单元顺序相反的数组
**语法:**
```php
array_reverse ( array $array [, bool $preserve_keys = FALSE ] ) : array
```

**说明:** 接受数组 array 作为输入并返回一个单元为相反顺序的新数组。
- 如果$preserve_keys设置为TRUE会保留数字的键。 非数字的键则不受这个设置的影响，总是会被保留

**代码:**

```php
<?php
$input  = array("php", 4.0, array("green", "red"));
$reversed = array_reverse($input);
$preserved = array_reverse($input, true);

print_r($reversed);
print_r($preserved);
?>
```

**输出:**
```php
Array
(
    [0] => Array
        (
            [0] => green
            [1] => red
        )

    [1] => 4
    [2] => php
)
Array
(
    [2] => Array
        (
            [0] => green
            [1] => red
        )

    [1] => 4
    [0] => php
)
```


## 2.7 [array_search 条件查找](https://www.php.net/manual/zh/function.array-search.php)
**功能:**  在数组中搜索给定的值，如果成功则返回首个相应的键名
**语法:**
```php
array_search ( mixed $needle , array $haystack [, bool $strict = false ] ) : mixed
```

**说明:** 
- 如果$strict为TRUE，则array_search() 将在 haystack 中检查完全相同的元素(===)
- 如果找到了needle则返回它的键，否则返回 FALSE。

**代码:**

```php
<?php
$array = array(0 => 'blue', 1 => 'red', 2 => 'green', 3 => 'red', 4=>5);

$key1 = array_search('green', $array); 
$key2 = array_search('red', $array); 
$key3 = array_search('5', $array,true);
var_dump($key1,$key2,$key3);
?>
```

**输出:**
```php
int(2)
int(1)
bool(false)
```


## 2.8 [array_slice 取出一段](https://www.php.net/manual/zh/function.array-slice.php)
**功能:**   从数组中取出一段
**语法:**
```php
array_slice ( array $array , int $offset [, int $length = NULL [, bool $preserve_keys = false ]] ) : array
```
**说明:** 
- 注意array_slice()默认会重新排序并重置数组的数字索引。你可以通过将 preserve_keys 设为 TRUE 来改变此行为
- 如果给出了length并且为正，则序列中将具有这么多的单元。
- 如果给出了length并且为负，则序列将终止在距离数组末端这么远的地方。
- 如果省略，则序列将从offset开始一直到array的末端。

**代码:**

```php
<?php
$input = array("a", "b", "c", "d", "e");

$output = array_slice($input, 2);      // returns "c", "d", and "e"
$output = array_slice($input, -2, 1);  // returns "d"
$output = array_slice($input, 0, 3);   // returns "a", "b", and "c"

// note the differences in the array keys
print_r(array_slice($input, 2, -1));
print_r(array_slice($input, 2, -1, true));
?>
```

**输出:**
```php
Array
(
    [0] => c
    [1] => d
)
Array
(
    [2] => c
    [3] => d
)
```


## 2.9 [array_values 所有值](https://www.php.net/manual/zh/function.array-values.php)
**功能:** 返回数组中所有的值
**语法:**
```php
array_values ( array $array ) : array
```
**说明:** 返回input数组中所有的值并给其建立数字索引

**代码:**

```php
<?php
$array = array("size" => "XL", "color" => "gold");
print_r(array_values($array));
?>
```

**输出:**
```php
Array
(
    [0] => XL
    [1] => gold
)
```

# 3.排序

## 3.1 [arsort 对值:逆向排序](https://www.php.net/manual/zh/function.arsort.php)
**功能:** 对数组进行逆向排序并保持索引关系
**语法:**
```php
arsort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : bool
```
**说明:** 对数组进行排序，数组的索引保持和单元的关联。

**代码:**

```php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");
arsort($fruits);
print_r($fruits);
```

**输出:**
```php
Array
(
   [a] => orange
   [d] => lemon
   [b] => banana
   [c] => apple
)
```

## 3.2 [asort 对值:正向排序](https://www.php.net/manual/zh/function.arsort.php)
**功能:** 对数组进行排序并保持索引关系
**语法:**
```php
asort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : bool
```
**说明:** 对数组进行排序，数组的索引保持和单元的关联。主要用于对那些单元顺序很重要的结合数组进行排序。

**代码:**

```php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");
asort($fruits);
print_r($fruits);
```

**输出:**
```php
Array
(
   [c] => apple
   [b] => banana
   [d] => lemon
   [a] => orange
)
```

## 3.3 [rsort 对值:逆向排序](https://www.php.net/manual/zh/function.rsort.php)
**功能:** 对数组进行逆向排序，不保留索引关系
**语法:**
```php
rsort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : bool
```
**说明:** 对数组进行逆向排序（最高到最低）。

**代码:**

```php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");
rsort($fruits);
print_r($fruits);
```

**输出:**
```php
Array
(
   [0] => orange
   [1] => lemon
   [2] => banana
   [3] => apple
)
```


## 3.4 [sort 对值:正向排序](https://www.php.net/manual/zh/function.sort.php)
**功能:** 对数组进行正向排序，不保留索引关系
**语法:**
```php
sort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : bool
```
**说明:** 对数组进行逆向排序（最高到最低）。

**代码:**

```php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");
sort($fruits);
print_r($fruits);
```

**输出:**
```php
Array
(
   [0] => apple
   [1] => banana
   [2] => lemon
   [3] => orange
)
```



## 3.5 [ksort 对key:正向排序](https://www.php.net/manual/zh/function.ksort.php)
**功能:** 对数组按照键名正向排序
**语法:**
```php
ksort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : bool
```
**说明:** 对数组按照键名正向排序，保留键名到数据的关联。主要用于结合数组。

**代码:**

```php
$fruits = array("d"=>"lemon", "a"=>"orange", "b"=>"banana", "c"=>"apple");
ksort($fruits);
print_r($fruits);
```

**输出:**
```php
Array
(
   [a] => orange
   [b] => banana
   [c] => apple
   [d] => lemon
)
```


## 3.6 [krsort  对key:逆向排序](https://www.php.net/manual/zh/function.krsort.php)
**功能:** 对数组按照键名逆向排序
**语法:**
```php
krsort ( array &$array [, int $sort_flags = SORT_REGULAR ] ) : bool
```
**说明:** 对数组按照键名逆向排序，保留键名到数据的关联。主要用于结合数组。

**代码:**

```php
$fruits = array("d"=>"lemon", "a"=>"orange", "b"=>"banana", "c"=>"apple");
krsort($fruits);
print_r($fruits);
```

**输出:**
```php
Array
(
   [d] => lemon
   [c] => apple
   [b] => banana
   [a] => orange
)
```


## 3.7 [array_multisort 多维数组排序](https://www.php.net/manual/zh/function.array-multisort.php)
**功能:** 返回数组中所有的值
**语法:**
```php
array_multisort ( array &$array1 [, mixed $array1_sort_order = SORT_ASC [, mixed $array1_sort_flags = SORT_REGULAR [, mixed $... ]]] ) : bool
```
**说明:** 可以用来一次对多个数组进行排序，或者根据某一维或多维对多维数组进行排序。
- 关联（string）键名保持不变，但数字键名会被重新索引
- array1_sort_order:参数要排列的顺序,SORT_ASC升序,SORT_DESC倒序,默认 SORT_ASC
- array1_sort_flags:
  - SORT_REGULAR - 将项目按照通常方法比较（不修改类型）
  - SORT_NUMERIC - 按照数字大小比较
  - SORT_STRING - 按照字符串比较


### 1. 一维数组排序

**代码:**
```php
$a=array("Dog","Cat","Horse","Bear","Zebra");
array_multisort($a);
print_r($a);
```
**输出:**
```php
Array
(
   [0] => Bear
   [1] => Cat
   [2] => Dog
   [3] => Horse
   [4] => Zebra
)
```
### 2. 多个一维数组排序

**代码:**
```php
$ar1 = array(10, 100, 100, 0);
$ar2 = array(1, 3, 2, 4);
array_multisort($ar1, $ar2);

var_dump($ar1);
var_dump($ar2);
```


说明: 可以把$ar1和$ar2的数据可以看成一个表 如下：

$ar1 | $ar2
--- | ---
10  | 1
100 | 3
100 | 2
0   | 4

> $ar1和$ar2分别为列名，然后像sql语句一样 排序即可
```sql
select * from table order by $ar1 asc ,$ar2 asc 
```


**输出:**
```php
array(4) {
  [0]=> int(0)
  [1]=> int(10)
  [2]=> int(100)
  [3]=> int(100)
}
array(4) {
  [0]=> int(4)
  [1]=> int(1)
  [2]=> int(2)
  [3]=> int(3)
}
```

### 3.多维数组排序

**原数据:**
```php
<?php
$data[] = array('volume' => 67, 'edition' => 2);
$data[] = array('volume' => 86, 'edition' => 1);
$data[] = array('volume' => 85, 'edition' => 6);
$data[] = array('volume' => 98, 'edition' => 2);
$data[] = array('volume' => 86, 'edition' => 6);
$data[] = array('volume' => 67, 'edition' => 7);
```
需求:将把 volume 降序排列，把 edition 升序排列。

**代码:**
```php
<?php
// 取得列的列表
foreach ($data as $key => $row) {
    $volume[$key]  = $row['volume'];
    $edition[$key] = $row['edition'];
}

// 将数据根据 volume 降序排列，根据 edition 升序排列
// 把 $data 作为最后一个参数，以通用键排序
array_multisort($volume, SORT_DESC, $edition, SORT_ASC, $data);
print_r($data);
?>
```

**输出:**
```php
Array
(
   [0] => Array
       (
           [volume] => 98
           [edition] => 2
       )
   [1] => Array
       (
           [volume] => 86
           [edition] => 1
       )
   [2] => Array
       (
           [volume] => 86
           [edition] => 6
       )
   [3] => Array
       (
           [volume] => 85
           [edition] => 6
       )
   [4] => Array
       (
           [volume] => 67
           [edition] => 2
       )
   [5] => Array
       (
           [volume] => 67
           [edition] => 7
       )
)
```


# 4.判断

## 4.1 [array_key_exists key是否存在](https://www.php.net/manual/zh/function.array-key-exists.php)
**功能:**  检查数组里是否有指定的键名或索引
**语法:**
```php
array_key_exists(mixed $key , array $array) : bool
```
**说明:** 
- 数组里有键key时，返回TRUE。 key可以是任何能作为数组索引的值。
- 仅仅搜索第一维的键。 多维数组里嵌套的键不会被搜索到。
- key_exists函数和此函数功能一样

**代码:**
```php
<?php
$search_array = array('first' => 1, 'second' => 4);
if (array_key_exists('first', $search_array)) {
    echo "key first 存在!";
} else {
    echo "key first 不存在!";
}
```

**输出:**
```php
key first 存在!
```

**array_key_exists()与isset()对比**

isset() 对于数组中为 NULL 的值不会返回 TRUE，而 array_key_exists() 会。

```php
<?php
$search_array = array('first' => null, 'second' => 4);

// returns false
isset($search_array['first']);

// returns true
array_key_exists('first', $search_array);

```


## 4.2 [in_array 值是否存在](https://www.php.net/manual/zh/function.in-array.php)
**功能:**  检查数组中是否存在某个值,如果找到 则返回TRUE，否则返回 FALSE。
**语法:**
```php
in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] ) : bool
```
**说明:** 
- needle:待搜索的值。
- haystack:待搜索的数组。
- strict: 如果第三个参数strict的值为TRUE则会严格匹配类型(===)

**代码:**
```php
<?php
// 非严格类型检查
$os = array("Mac", "NT", "Irix", "Linux","1");
if (in_array("Irix", $os)) {
    echo "Got Irix";
}
// 严格类型检查
if (in_array(1, $os,true)) {
    echo "存在数字 1";
} else {
    echo "不存在数字 1";
}

```
**输出:**
```php
Got Irix

不存在数字 1
```

# 5.计算

## 5.1 [array_count_values 统计值出现次数](https://www.php.net/manual/zh/function.array-count-values.php)
**功能:**   统计数组中所有的值。
**语法:**
```php
array_count_values ( array $array ) : array
```
**说明:** 
结果返回一个数组:数组的键是array里单元的值； 数组的值是array单元的值出现的次数。

**代码:**
```php
<?php
$array = array(1, "hello", 1, "world", "hello");
print_r(array_count_values($array));
```
**输出:**
```php
Array
(
    [1] => 2
    [hello] => 2
    [world] => 1
)
```

## 5.2 [array_diff_assoc 数组值的差集](https://www.php.net/manual/zh/function.array-diff-assoc.php)
**功能:** 带索引检查计算数组的差集。(比较value和key)
**语法:**
```php
array_diff_assoc ( array $array1 , array $array2 [, array $... ] ) : array
```
**说明:** 
array_diff_assoc()返回一个数组，该数组包括了所有在array1中但是不在任何其它参数数组中的值。注意和array_diff()不同的是键名也用于比较。
**代码:**
```php
<?php
$array1 = array("a" => "green", "b" => "brown", "c" => "blue", "red");
$array2 = array("a" => "green", "yellow", "red");
$result = array_diff_assoc($array1, $array2);
print_r($result);
```
**输出:**
```php
Array
(
    [b] => brown
    [c] => blue
    [0] => red
)
```
**分析:**
上面的例子中可以看到键值对 "a" => "green" 在两个数组中都有，因此不在结果中的输出。与此不同，键值对 0 => "red" 出现在输出中是因为第二个参数中的 "red" 的键名是 1。

## 5.3 [array_diff_key 数组key的差集](https://www.php.net/manual/zh/function.array-diff-key.php)
**功能:** 使用键名比较计算数组的差集。
**语法:**
```php
array_diff_key(array $array1 ,array $array2 [, array $... ] ) : array
```
**说明:** 
array_diff_key()返回一个数组，该数组包括了所有出现在array1中但是未出现在任何其它参数数组中的键名的值。
**代码:**
```php
<?php
$array1 = array('blue'  => 1, 'red'  => 2, 'green'  => 3, 'purple' => 4,'0'=>'test');
$array2 = array('green' => 5, 'blue' => 6, 'yellow' => 7, 'cyan'   => 8 ,'test');
print_r(array_diff_key($array1, $array2));
```
**输出:**
```php
Array
(
    [red] => 2
    [purple] => 4
)
```
**注意:**
在 key => value 对中的两个键名仅在 (string) $key1 === (string) $key2 时被认为相等。换句话说，执行的是严格类型检查，因此字符串的表达必须完全一样。

## 5.4 [array_diff 数组差集](https://www.php.net/manual/zh/function.array-diff.php)
**功能:** 计算数组的差集。(比较key)
**语法:**
```php
array_diff(array $array1 , array $array2 [, array $... ] ) : array
```
**说明:** 
对比array1和其他一个或者多个数组，返回在array1中但是不在其他array里的值。
**代码:**
```php
<?php
$array1 = array("a" => "green", "red", "blue", "red");
$array2 = array("b" => "green", "yellow", "red");
$result = array_diff($array1, $array2);

print_r($result);
```
**输出:**
```php
Array
(
    [1] => blue
)
```


## 5.5 [array_intersect_assoc 数组值的交集](https://www.php.net/manual/zh/function.array-intersect-assoc.php)
**功能:** 带索引检查计算数组的交集。(比较value和key)
**语法:**
```php
array_intersect_assoc(array $array1 , array $array2 [, array $... ] ):array
```
**说明:** 
返回一个数组，该数组包含了所有在array1中也同时出现在所有其它参数数组中的值。注意和array_intersect()不同的是键名也用于比较。
**代码:**
```php
<?php
$array1 = array("a" => "green", "b" => "brown", "c" => "blue", "red");
$array2 = array("a" => "green", "b" => "yellow", "blue", "red");
$result_array = array_intersect_assoc($array1, $array2);
print_r($result_array);
```
**输出:**
```php
Array
(
    [a] => green
)
```

## 5.6 [array_intersect 数组值的交集](https://www.php.net/manual/zh/function.array-intersect.php)
**功能:** 计算数组的交集。(只比较value)
**语法:**
```php
array_intersect(array $array1 ,array $array2 [, array $... ] ) : array
```
**说明:** 
返回一个数组，该数组包含了所有在array1中也同时出现在所有其它参数数组中的值。注意键名保留不变。
**代码:**
```php
<?php
$array1 = array("a" => "green", "red", "blue");
$array2 = array("b" => "green", "yellow", "red");
$result = array_intersect($array1, $array2);
print_r($result);
```
**输出:**
```php
Array
(
    [a] => green
    [0] => red
)
```

## 5.7 [array_intersect_key 数组key的交集](https://www.php.net/manual/zh/function.array-intersect.php)
**功能:** 使用键名比较计算数组的交集。(只比较key)
**语法:**
```php
array_intersect_key(array $array1 ,array $array2 [, array $... ] ):array
```
**说明:** 
返回一个数组，该数组包含了所有出现在array1中并同时出现在所有其它参数数组中的键名的值。
**代码:**
```php
<?php
$array1 = array('blue'  => 1, 'red'  => 2, 'green'  => 3, 'purple' => 4);
$array2 = array('green' => 5, 'blue' => 6, 'yellow' => 7, 'cyan'   => 8);
print_r(array_intersect_key($array1, $array2));
```
**输出:**
```php
Array
(
    [blue] => 1
    [green] => 3
)
```


## 5.8 [array_product 值的乘积](https://www.php.net/manual/zh/function.array-product.php)
**功能:** 计算数组中所有值的乘积
**语法:**
```php
array_product(array $array ) : number
```
**说明:** 
以整数或浮点数返回一个数组中所有值的乘积。
**代码:**
```php
<?php
$a = array(2, 4, 6, 8);
echo "array_product(a) = " . array_product($a) . "\n";
echo "array_product(array()) = " . array_product(array()) . "\n";
```
**输出:**
```php
array_product(a) = 384
array_product(array()) = 1
```

## 5.9 [array_sum 值求和](https://www.php.net/manual/zh/function.array-sum.php)
**功能:** 对数组中所有值求和
**语法:**
```php
array_sum ( array $array ) : number
```
**说明:** 
将数组中的所有值相加，并返回结果。
**代码:**
```php
<?php
$a = array(2, 4, 6, 8);
echo "sum(a) = " . array_sum($a) . "\n";

$b = array("a" => 1.2, "b" => 2.3, "c" => 3.4);
echo "sum(b) = " . array_sum($b) . "\n";
```
**输出:**
```php
sum(a) = 20
sum(b) = 6.9
```

# 6. 递归
## 6.1 [array_merge_recursive 合并](https://www.php.net/manual/zh/function.array-merge-recursive.php)
**功能:** 将一个或多个数组的单元合并起来，一个数组中的值附加在前一个数组的后面。返回作为结果的数组。
**说明:** 如果输入的数组中有相同的字符串键名，则这些值会被合并到一个数组中去，这将递归下去，因此如果一个值本身是一个数组，本函数将按照相应的条目把它合并为另一个数组。需要注意的是，如果数组具有相同的数值键名，后一个值将不会覆盖原来的值，而是附加到后面。
**语法:**
```php
array_merge_recursive ( array $array1 [, array $... ] ) : array
```
**代码:**

```php
<?php
$ar1 = array("color" => array("favorite" => "red"), 5);
$ar2 = array(10, "color" => array("favorite" => "green", "blue"));
$result = array_merge_recursive($ar1, $ar2);
print_r($result);
?>
```
**输出:**

```php
Array
(
    [color] => Array
        (
            [favorite] => Array
                (
                    [0] => red
                    [1] => green
                )

            [0] => blue
        )

    [0] => 5
    [1] => 10
)
```



## 6.2 [array_replace_recursive 替换](https://www.php.net/manual/zh/function.array-replace-recursive.php)
**功能:** 使用传递的数组递归替换第一个数组的元素
**语法:**
```php
array_replace_recursive(array $array1 [, array $... ] ) : array
```
**说明:** 使用后面数组元素的值替换数组 array1 的值
- 如果一个键存在于第一个数组同时也存在于第二个数组，它的值将被第二个数组中的值替换
- 如果一个键存在于第二个数组，但是不存在于第一个数组，则会在第一个数组中创建这个元素。 
- 如果一个键仅存在于第一个数组，它将保持不变。 
- 如果传递了多个替换数组，它们将被按顺序依次处理，后面的数组将覆盖之前的值


**代码:**
```php
<?php
$base = array('citrus' => array( "orange") , 'berries' => array("blackberry", "raspberry"), );
$replacements = array('citrus' => array('pineapple'), 'berries' => array('blueberry'));

$basket = array_replace_recursive($base, $replacements);
print_r($basket);

?>
```

**输出:**
```php
Array
(
    [citrus] => Array
        (
            [0] => pineapple
        )

    [berries] => Array
        (
            [0] => blueberry
            [1] => raspberry
        )

)
```



## 6.3 [array_map 遍历](https://www.php.net/manual/zh/function.array-map.php)
**功能:** 为数组的每个元素应用回调函数
**语法:**
```php
array_map(callable $callback ,array $array1 [, array $... ] ):array
```
**说明:** 
返回数组，是为array1每个元素应用callback函数之后的数组。 callback函数形参的数量和传给array_map()数组数量，两者必须一样。


**代码:**
```php
<?php
function cube($n)
{
    return($n * $n * $n);
}

$a = array(1, 2, 3, 4, 5);
$b = array_map("cube", $a);
print_r($b);

// 匿名函数
$a = array(1, 2, 3, 4, 5);
$sign = 10;
$b2 = array_map(function (&$value) use ($sign){
    return $value * $sign;
},$a);
print_r($b2);

?>
```

**输出:**
```php
Array
(
    [0] => 1
    [1] => 8
    [2] => 27
    [3] => 64
    [4] => 125
)

// 匿名函数输出
Array
(
    [0] => 10
    [1] => 20
    [2] => 30
    [3] => 40
    [4] => 50
)
```



## 6.4 [array_walk 遍历](https://www.php.net/manual/zh/function.array-walk.php)
**功能:** 使用用户自定义函数对数组中的每个元素做回调处理
**语法:**
```php
array_walk(array &$array,callable $callback [, mixed $userdata = NULL ]):bool
```
**说明:** 
- 如果提供了可选参数userdata，将被作为第三个参数传递给callback funcname
- callback 接受两个参数。array参数的值作为第一个，键名作为第二个。
- 只有array的值才可以被改变，用户不应在回调函数中改变该数组本身的结构。例如增加/删除单元，unset 单元等等

**代码:**
```php
<?php
$fruits = array("d" => "lemon", "a" => "orange", "b" => "banana", "c" => "apple");
function test_alter(&$item1, $key, $prefix)
{
    $item1 = "$prefix: $item1";
}

function test_print($item2, $key)
{
    echo "$key. $item2<br />\n";
}
echo "Before ...:\n";
array_walk($fruits, 'test_print');

array_walk($fruits, 'test_alter', 'fruit');
echo "... and after:\n";
array_walk($fruits, 'test_print');


// 匿名函数调用
$a = array(1, 2, 3, 4, 5, 8);
$sign = 10;
array_walk($a,function (&$val,$key,$sign){
    if ($key > 2){
        $val *= $sign;
    }
},$sign);
print_r($a);
?>
```

**输出:**
```php
Before ...:
d. lemon
a. orange
b. banana
c. apple
... and after:
d. fruit: lemon
a. fruit: orange
b. fruit: banana
c. fruit: apple

// 匿名函数调用输出
Array
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 40
    [4] => 50
    [5] => 80
)
```


## 6.5 [array_walk_recursive 遍历](https://www.php.net/manual/zh/function.array-walk-recursive.php)
**功能:** 对数组中的每个成员递归地应用用户函数，(本函数会递归到更深层的数组中去)
**语法:**
```php
array_walk_recursive(array &$array ,callable $callback [, mixed $userdata = NULL ]):bool
```
**说明:** 
- 如果提供了可选参数userdata，将被作为第三个参数传递给callback funcname
- callback 接受两个参数。array参数的值作为第一个，键名作为第二个。

**代码:**
```php
<?php
$sweet = array('a' => 'apple', 'b' => 'banana');
$fruits = array('sweet' => $sweet, 'sour' => 'lemon');

function test_print($item, $key)
{
    echo "$key holds $item\n";
}

array_walk_recursive($fruits, 'test_print');

// 匿名函数使用
$sweet = array('a' => 'apple', 'b' => 'banana');
$fruits = array('sweet' => $sweet, 'sour' => 'lemon');
$str = '===>';
array_walk_recursive($fruits,function ($val,$key,$str){
        echo "$key $str $val \n";
},$str);
?>
```

**输出:**
```php
a holds apple
b holds banana
sour holds lemon

// 匿名函数调用输出
a ===> apple 
b ===> banana 
sour ===> lemon 

```

# 7. 赋值与变量

## 7.1 [list 给一组变量赋值](https://www.php.net/manual/zh/function.list.php)
**功能:** 把数组中的值赋给一组变量
**语法:**
```php
list(mixed $var1 [, mixed $... ] ):array
```
**说明:** 
- 像array()一样，这不是真正的函数，而是语言结构。 list()可以在单次操作内就为一组变量赋值。
- 在PHP7.1.0之前的版本，list()仅能用于数字索引的数组，并假定数字索引从0开始。
- PHP5里，list()从最右边的参数开始赋值； PHP7里，list()从最左边的参数开始赋值

- 常用例子 
**代码:**

```php
<?php
$info = array('coffee', 'brown', 'caffeine');

// 列出所有变量
list($drink, $color, $power) = $info;
echo "$drink is $color and $power makes it special.\n";

// 列出他们的其中一个
list($drink, , $power) = $info;
echo "$drink has $power.\n";

// 或者让我们跳到仅第三个
list( , , $power) = $info;
echo "I need $power!\n";

// list() 不能对字符串起作用
list($bar) = "abcde";
var_dump($bar); // NULL
?>
```
- 嵌套的list() 
**代码:**
```php

list($a, list($b, $c)) = array(1, array(2, 3));

var_dump($a, $b, $c);
```

**输出:**
```php
int(1)
int(2)
int(3)
```

## 7.2 [compact 创建](https://www.php.net/manual/zh/function.compact.php)
**功能:** 建立一个数组，包括变量名和它们的值
**说明:** 对每个参数，compact() 在当前的符号表中查找该变量名并将它添加到输出的数组中，变量名成为键名而变量的内容成为该键的值。简单说，它做的事和 [extract()](https://www.php.net/manual/zh/function.extract.php) 正好相反。返回将所有变量添加进去后的数组。
**语法:**
```php
compact ( mixed $varname1 [, mixed $... ] ) : array
```

**代码:**

```php
<?php
$city  = "San Francisco";
$state = "CA";
$event = "SIGGRAPH";

$location_vars = array("city", "state");

$result = compact("event", "nothing_here", $location_vars);
print_r($result);
?>
```

**输出:**
```php
Array
(
    [event] => SIGGRAPH
    [city] => San Francisco
    [state] => CA
)
```


## 7.3 [extract 导出为变量](https://www.php.net/manual/zh/function.extract.php)
**功能:** 从数组中将变量导入到当前的符号表
**说明:** 
- array:一个关联数组。此函数会将键名当作变量名，值作为变量的值。 对每个键／值对都会在当前的符号表中建立变量，并受到 flags 和 prefix 参数的影响。
- flags
  - EXTR_OVERWRITE : 如果有冲突，覆盖已有的变量
  - EXTR_SKIP: 如果有冲突，不覆盖已有的变量。
  - EXTR_PREFIX_SAME: 如果有冲突，在变量名前加上前缀prefix。
  - EXTR_PREFIX_ALL: 给所有变量名加上前缀 prefix。
  ....
**语法:**
```php
extract(array &$array [, int $flags = EXTR_OVERWRITE [, string $prefix = NULL ]]):int
```

**代码:**

```php
<?php

/* 假定 $var_array 是 wddx_deserialize 返回的数组*/

$size = "large";
$var_array = array("color" => "blue",
                   "size"  => "medium",
                   "shape" => "sphere");
extract($var_array, EXTR_PREFIX_SAME, "wddx");

echo "$color, $size, $shape, $wddx_size\n";
?>
```

**输出:**
```php
blue, large, sphere, medium
```
$size 没有被覆盖，因为指定了 EXTR_PREFIX_SAME，这使得 $wddx_size 被建立。如果指定了 EXTR_SKIP，则 $wddx_size 也不会被建立。