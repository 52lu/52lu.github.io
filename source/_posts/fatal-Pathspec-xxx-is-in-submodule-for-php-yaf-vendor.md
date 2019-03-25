---
title: 'fatal: Pathspec ''xxx'' is in submodule for php yaf vendor'
date: 2018-08-13 11:33:58
tags:
 - php
 - yaf
 - git 
categories:
- git
---
### 使用YAF进行项目开发，在手动更新vendor的时候发现更新以后无法git add。


使用git status查看，没有任何提交，后手动删除重新copy了文件，再次使用git status：

```
modified: vendor/phpgangsta/googleauthenticator/PHPGangsta/GoogleAuthenticator.php (modified content)
```

git add后只增加了文件夹，但是没有文件。
手动Add: 
```
git vendor/phpgangsta/googleauthenticator/PHPGangsta/GoogleAuthenticator.php

报出错误信息： 
fatal: Pathspec 'xxx' is in submodule

```
<!--more-->
### 解决方法
- 发现vendor/phpgangsta/googleauthenticator/PHPGangsta/下 有.git文件 
-  所以使用下面命令： 
```
git rm -rf --cached vendor/phpgangsta/googleauthenticator/PHPGangsta
git add vendor/phpgangsta/*
```
然后正常提交。