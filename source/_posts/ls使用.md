---
title: 命令-ls
date: 2015-08-13 12:21:14
tags:
 - linux
categories:
 - 命令使用
---


# 1.使用语法
```
ls（选项）（参数）
```
# 2.参数说明
```
-a 列出目录下的所有文件，包括以 . 开头的隐含文件。

-A 显示除 “.”和“..”外的所有文件

-b 把文件名中不可输出的字符用反斜杠加字符编号的形式列出。

-B 不输出以 “~”结尾的备份文件。

-c 输出文件的 i 节点的修改时间，并以此排序。

-C 按列输出，纵向排序。

-d 将目录象文件一样显示，而不是显示其下的文件。

-e 该ls命令选项可以输出时间的全部信息，而不是输出简略信息。

-f -U 该ls命令选项可以对输出的文件不排序。

-i 该ls命令选项可以输出文件的 i 节点的索引信息。

-k 该ls命令选项可以以 k 字节的形式表示文件的大小。

-l 该ls命令选项可以列出文件的详细信息。

-m 该ls命令选项可以横向输出文件名，并以“，”作分格符。

-n 该ls命令选项可以用数字的GUID代替名称。

-o 该ls命令选项可以显示文件的除组信息外的详细信息。

-p -F 该ls命令选项可以在每个文件名后附上一个字符以说明该文件的类型，“*”表示可执行的普通文件；“/”表示目录；“@”表示符号链接；“|”表示FIFOs；“=”表示套接字(sockets)。

-q 该ls命令选项可以用?代替不可输出的字符。

-r 该ls命令选项可以对目录反向排序。

-s 该ls命令选项可以在每个文件名后输出该文件的大小。

-t 该ls命令选项可以以时间排序。

-u 该ls命令选项可以以文件上次被访问的时间排序。

-x 该ls命令选项可以按列输出，横向排序。

-G 该ls命令选项可以输出文件的组的信息。

-L 该ls命令选项可以列出链接文件名而不是链接到的文件。

-N 该ls命令选项将不限制文件长度。

-Q 该ls命令选项可以把输出的文件名用双引号括起来。

-R 该ls命令选项可以列出所有子目录下的文件。

-S 该ls命令选项可以以文件大小排序。

-X 该ls命令选项可以以文件的扩展名(最后一个 . 后的字符)排序。

-1 该ls命令选项可以一行只输出一个文件。
```

# 3.使用示例
## 3.1 排序

- 按照大小排序显示文件
```
➜  ls -Slh

total 4040
-rw-r--r--  1 liuqh  staff   596K  6  1 11:04 inviteheader.png
-rw-r--r--  1 liuqh  staff   458K  6  1 11:04 rice_center.png
-rw-r--r--  1 liuqh  staff   103K  5 21 21:53 pk-share.png
-rw-rw-rw-@ 1 liuqh  staff    97K  6  2 05:41 background_morning.png
-rw-rw-rw-@ 1 liuqh  staff    92K  6  2 05:41 background_night.png
-rw-r--r--  1 liuqh  staff    91K  6  2 14:23 find_invite.png
-rw-r--r--  1 liuqh  staff    85K  6  1 11:04 pk.png
-rw-r--r--  1 liuqh  staff    83K  6  1 11:04 exchange.png
-rwxr-xr-x  1 liuqh  staff    72K  6  1 11:04 runner.png
-rw-r--r--  1 liuqh  staff    67K  6  2 12:13 bottomBG.png
-rw-r--r--  1 liuqh  staff    44K  6  1 11:04 fuceng.png
-rw-r--r--  1 liuqh  staff    41K  4 23 19:49 title@2x.png
-rw-r--r--@ 1 liuqh  staff    38K  6  3 17:18 win-jifen.png
-rw-r--r--@ 1 liuqh  staff    37K  6  3 17:18 win-rice.png
-rwxr-xr-x  1 liuqh  staff    13K  4  9 11:00 Badge@2x.png
-rwxr-xr-x  1 liuqh  staff   8.0K  4  9 11:00 bg@2x.png
...
```