---
title: PHP常用数组函数整理
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

## 1.3 [array_merge_recursive 合并](https://www.php.net/manual/zh/function.array-merge-recursive.php)
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



## 1.11 [array_replace_recursive 替换](https://www.php.net/manual/zh/function.array-replace-recursive.php)
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


## 1.16 [array_unique 去重](https://www.php.net/manual/zh/function.array-unique.php)
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



## 1.17 [shuffle 打乱](https://www.php.net/manual/zh/function.shuffle.php)
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


## 1.18 [compact 创建](https://www.php.net/manual/zh/function.compact.php)
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


## 3.x [array_multisort 多维数组排序](https://www.php.net/manual/zh/function.array-multisort.php)
**功能:** 返回数组中所有的值
**语法:**
```php
array_multisort ( array &$array1 [, mixed $array1_sort_order = SORT_ASC [, mixed $array1_sort_flags = SORT_REGULAR [, mixed $... ]]] ) : bool
```
**说明:** 可以用来一次对多个数组进行排序，或者根据某一维或多维对多维数组进行排序。
- 关联（string）键名保持不变，但数字键名会被重新索引

**代码:**

```php

```

**输出:**
```php
```