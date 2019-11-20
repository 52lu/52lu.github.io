---
title: Linux docker 搭建禅道
date: 2017-01-14
tags:
 - docker
categories:
 - 服务搭建

 
---

### 一、下载地址

禅道开源版：http://dl.cnezsoft.com/zentao/docker/docker_zentao.zip

数据库用户名：root,默认密码： 123456。运行时，可以设置 MYSQL_ROOT_PASSWORD变量来更改密码。

可挂载目录

/app/zentaopms:该目录为禅道目录，里面包含禅道代码及附件上传目录。

/var/lib/mysql:该目录为数据库的数据目录。

### 二、安装使用
<span style="color:red">注意：需要关闭下selinux </span>
<!--more-->
1、构建镜像

下载安装包，解压缩。 进入docker_zentao目录，执行命令 docker build -t [镜像名称] [Dockerfile所在目录]
```$xslt
docker build -t zentao ./
```
2、运行镜像
```$xslt
docker run --name [容器名称] -p [主机端口]:80 -v [主机代码目录]:/app/zentaopms -v [主机数据目录]:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=[数据库密码] -d [镜像名]:latest
```
例如
创建 /data/www /data/data 目录。

执行命令：
```$xslt
docker run --name zentao -p 80:80 -v /data/www:/app/zentaopms -v /data/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d zentao:latest
运行成功
```

3、安装禅道

浏览器访问 http://ip，显示禅道安装页面，安装禅道。

[原文参考](https://www.zentao.net/download/80098.html)