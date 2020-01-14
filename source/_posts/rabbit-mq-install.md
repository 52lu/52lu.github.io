---
title: 服务-RabbitMQ安装方法总结
date: 2016-08-03
tags:
 - rabbitmq
categories:
 - 服务搭建
---

具体安装如下：

### 一：安装Erlang：
 
 
```$xslt
  
 # rpm --import http://binaries.erlang-solutions.com/debian/erlang_solutions.asc  
  
 # cd /etc/yum.repos.d/ 
  
 # wget http://binaries.erlang-solutions.com/rpm/centos/erlang_solutions.repo  
  
 # yum install esl-erlang  
```

<!--more-->
### 二：安装esl-erlang-compat：
```$xslt
# cd /tmp/  
  
# wget https://raw.github.com/jasonmcintosh/esl-erlang-compat/master/rpmbuild/RPMS/noarch/esl-erlang-compat-R14B-1.el6.noarch.rpm   
  
# yum install esl-erlang-compat-R14B-1.el6.noarch.rpm 
 

```

### 三：安装RabbitMQ
 
 ```$xslt
 # cd /tmp 
 
 #  wget https://www.rabbitmq.com/releases/rabbitmq-server/v3.6.2/rabbitmq-server-3.6.2-1.noarch.rpm 
 
 # yum install rabbitmq-server-3.6.2-1.noarch.rpm 
```

  
到此，RabbitMQ基本安装完成了。
 
 
 
### 四：RabbitMQ页面监控系统
RabbitMQ提供了一个web的监控页面系统，这个系统是以Plugin的方式进行调用的。
在Documentation下的Server下的Management是关于配置这个插件的。地址：http://www.rabbitmq.com/management.html
这个管理插件是包含在RabbitMQ发行包里的，所以只需激活即可。
 
命令： 
```$xslt
# rabbitmq-plugins enable rabbitmq_management
```

 
### 五：配置登陆用户
 
 ```$xslt
# rabbitmqctl  add_user leyue leyue100
# rabbitmqctl  set_user_tags leyue administrator
# rabbitmqctl  set_permissions -p / leyue ".*" ".*" ".*"
```

 
浏览器地址栏输入：http://localhost:15672  用户名登陆
 
 
 
RabbitMQ服务启动、关闭、重启
```$xslt
# service rabbitmq-server start  //开启

# service rabbitmq-server stop   //关闭

#service rabbitmq-server restart //重启
```

