---
title: 'MacBook brew yaf error: unknown type name ''HASHKIT_API'''
date: 2018-07-31 19:47:18
tags:
 - php
 - mac
categories:
 - php 
 
---

问题：在 mac 系统中安装 PHP yaf 扩展时，总是失败；报错：error: unknown type name 'HASHKIT_API'

<!--more-->
```$xslt
\h:\W \u$ brew install php70-yaf
==> Installing php70-yaf from homebrew/php
==> Downloading https://github.com/laruence/yaf/archive/yaf-3.0.3.tar.gz
Already downloaded: /Users/liuqh/Library/Caches/Homebrew/php70-yaf-3.0.3.tar.gz
==> /usr/local/opt/php70/bin/phpize
==> ./configure --prefix=/usr/local/Cellar/php70-yaf/3.0.3 --with-php-config=/usr/local/opt/php70/bin/php-config
==> make
Last 15 lines from /Users/liuqh/Library/Logs/Homebrew/php70-yaf/03.make:
/usr/local/include/string.h:56:1: error: unknown type name 'HASHKIT_API'
HASHKIT_API
^
/usr/local/include/string.h:57:1: error: expected identifier or '('
const char *hashkit_string_c_str(const hashkit_string_st* self);
^
6 errors generated.
make: *** [yaf_bootstrap.lo] Error 1
make: *** Waiting for unfinished jobs....
6 errors generated.
make: *** [yaf.lo] Error 1
6 errors generated.
make: *** [yaf_application.lo] Error 1
6 errors generated.
make: *** [yaf_dispatcher.lo] Error 1

```

====> 解决方法：
找到/usr/local/include/string.h 注释调和'HASHKIT_API有关的代码；

```$xslt
/*=============================20180314 解决yaf 装不上的问题，注释
#HASHKIT_API
void hashkit_string_free(hashkit_string_st *ptr);

#HASHKIT_API
size_t hashkit_string_length(const hashkit_string_st *self);

#HASHKIT_API
const char *hashkit_string_c_str(const hashkit_string_st* self);

#================================================================
*/
```


补充：以上的解决方法有问题，虽然 phpinfo 能显示 yaf 扩展；但是实际使用中会报错；


```$xslt
>>> nginx 报错：
     2018/03/16 12:01:44 [error] 4965#0: *25 kevent() reported about an closed connection (54: Connection reset by peer) while reading response header from upstream, client: 127.0.0.1, server: 127.0.0.1, request: "GET /test/lqh/test?XDEBUG_SESSION_START=19833 HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "www.cloudins.com"

>>>php-fpm报错:

[16-Mar-2018 12:21:27] NOTICE: fpm is running, pid 6593
[16-Mar-2018 12:21:27] NOTICE: ready to handle connections
[16-Mar-2018 12:22:01] WARNING: [pool www] child 6594 exited on signal 11 (SIGSEGV) after 34.532384 seconds from start
[16-Mar-2018 12:22:01] NOTICE: [pool www] child 6602 started
[16-Mar-2018 14:10:02] NOTICE: Finishing ...
[16-Mar-2018 14:10:02] NOTICE: exiting, bye-bye!

```

后来发现不只是安装yaf会保这个错，安装一些其他的服务也会报这个错，
##### 解决方法：
``` 
把/usr/local/include 临时改为 :/usr/local/include_bak
```



结论：
```
这些错误信息各种 google，都没有对应的解决方案，只是遇到同样问题的人；
后来最后的解决方法是从同事电脑 （MacBook Pro）brew安装好的包直接引入;
我的电脑是（MacBook），个人猜测是电脑系统（/usr/local/include）中文件的问题，因为我发现MacBook Pro该目录中没有这些文件；

```

