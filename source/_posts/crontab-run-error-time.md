---
title: 发现cron不生成日志文件或不按时执行怎么办?
date: 2015-10-03 11:42:44
tags:
  - crond
categories:
  - 入坑历史
---

# 1.在/var/log/ 没有cron日志文件

**处理方法:**

```$xslt
# yum install rsyslog 
```


# 2.没有按时执行

**处理方法:**

- 修改时区

```$xslt
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

- 重启服务

```$xslt
# service crond restart
# service rsyslog restart
```


# 3.在线生成定时任务
[点击,去生成](https://crontab-generator.org/)

![](https://52lu.github.io/directionsImg/other/crontab-make.png)


