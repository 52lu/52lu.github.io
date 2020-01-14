---
title: 服务-定时任务(crontab)使用总结
date: 2015-10-03 11:42:44
tags:
  - crond
categories:
  - 入坑历史
---


# 1.crontab格式

![](https://52lu.github.io/directionsImg/linux/crontab.png)

## 1.1 示例
```sh
# 每10分钟,运行一次命令
*/10 * * * * command

# 每一小时,运行一次命令
* */1 * * * command

# 每天晚上的21:30,运行一次命令
30 21 * * * command

# 每天18:00至22:00之间每隔30分钟,运行一次命令
0,30 18-22 * * * command

# 在23:00~07:00之间,运行一次命令
* 23-7/1 * * * command

# 每个工作日(周一 ~ 周五)晚上21:30,运行一次命令
30 21 * * 1-5 command

# 每星期六的23:00,运行一次命令
0 23 * * 6 command

# 每周六、周日的 2:30,运行一次命令
30 2 * * 6,0 /usr/local/etc/rc.d/lighttpd restart

# 每个月的第一天早上08:10,运行一次命令
10 8 1 * * command

# 每个月1、10、22日的5:00,运行一次命令
0 5 1,10,22 * * command
```


# 2.踩坑点
## 2.1 在/var/log/ 没有cron日志文件

**处理方法:**

```$xslt
# yum install rsyslog 
```


## 2.2 没有按时执行

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
![](https://52lu.github.io/directionsImg/other/crontab-make.png)
[点击,去生成](https://crontab-generator.org/)



