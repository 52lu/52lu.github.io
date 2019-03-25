---
title: php 正则去掉字符串中非汉字字符串
date: 2017-03-09 10:05:37
tags:
 - php
categories:
 - php 
---

直接上代码示例：

```$xslt

$str='2016，北京欢迎您 from around you..';
 
preg_match_all('/[\x{4e00}-\x{9fff}]+/u', $str, $matches);
 
$str = join('', $matches[0]);
 
```

输出
 
```$xslt
北京欢迎您
```
