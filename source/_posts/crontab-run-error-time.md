---
title: crontab 无运行日志文件或不按时执行
date: 2015-10-03 11:42:44
tags:
  - crond
categories:
  - 入坑历史
---

### Centos7 crontab 没有按时执行任务，并且在/var/log/ 没有cron日志文件

#### 1.没有日志文件解决方法：
```
# yum install rsyslog 
```


#### 2.没有按时执行解决方法:
```

# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 然后重启下面服务 
# service crond restart
# service rsyslog restart
```

