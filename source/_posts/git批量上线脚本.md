---
title: git批量上线脚本
date: 2019-08-16 12:44:05
tags:
categories:
 - git
---

#### online.sh
上线执行的脚本
```
#!/bin/sh
pssh -h ./demo.list -l root -P "cd /xxx/xxx/demo && git pull"

```

#### demo.list
该文件下指的是上线哪些服务器的列表
```
root@xxxx.xx.xx.01
root@xxxx.xx.xx.02
root@xxxx.xx.xx.03
root@xxxx.xx.xx.04
```