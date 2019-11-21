---
title: Mysql8问题归纳
date: 2019-08-03 10:16:09
tags:
 - mysql
categories:
 - 入坑历史
---

#### 报错：PDO::__construct(): Server sent charset (255) unknown to the client. Please, report to the developers

根据网上资料显示，是由于Mysql8.0将默认的字符集改为了utfmb4，因此和客户端（不仅仅是PHP）的通信无法识别，我们需要更改my.cnf来指定字符集。

```$xslt
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
collation-server = utf8_unicode_ci
character-set-server = utf8

```

#### 报错：PDO::__construct(): The server requested authentication method unknown to the client [caching_sha2_password]

根据[网上资料](https://stackoverflow.com/questions/49083573/php-7-2-2-mysql-8-0-pdo-gives-authentication-method-unknown-to-the-client-ca)显示，是由于用户身份认证的加密方式不兼容导致的，mysql8.0中默认方式为caching_sha2_password，引起老版本兼容性问题，老版本加密方式为mysql_native_password。

新建用老版加密方式初始化密码的用户即可：

```$xslt
CREATE USER username@localhost identified with mysql_native_password by 'password';
```



