---
title: Maven私服搭建(nexus3)和使用
date: 2019-11-24 00:04:08
tags:
 - maven
 - nexus
categories:
 - 服务搭建
---

# 1. 使用docker安装

## 1.1 编写docker-compose.yml 
**文件位置**: /service

**具体内容:**

```
version: '3.1'
services:
  #服务名称:docker-compose exec 服务名称 
  nexus:
    restart: always
    image: sonatype/nexus3
    #容器名称
    container_name: nexus3 
    ports:
      - 8081:8081
    volumes:
    # ${DATA_PATH} 变量信息，放在了.env 里
      - ${DATA_PATH}/nexus:/nexus-data
```
## 1.2 编写.env 配置文件
**文件位置**: /service

**具体内容:**

```
#服务数据目录
DATA_PATH=/service
```

## 1.3 创建目录

创建对应的服务目录:/service/nexus,用来保存nexus服务的相关数据信息，并设置权限为可读写

```
# 创建目录
root@hui-X555LD /service » mkdir nexus
# 设置权限
root@hui-X555LD /service » chmod 777 nexus
```

目录信息总览:

```
root@hui-X555LD /service » pwd
/service
root@hui-X555LD /service » ls -la
总用量 20
drwxrwxrwx  3 root root 4096 11月 23 23:23 .
drwxr-xr-x 25 root root 4096 11月 23 23:10 ..
-rw-r--r--  1 root root  189 11月 23 23:10 docker-compose.yml
-rwxrwxrwx  1 root root   39 11月 23 23:10 .env
drwxrwxrwx 15 root root 4096 11月 23 23:18 nexus
```


# 2. 启动nexus3

![](https://mrliuqh.github.io/directionsImg/service/nexus/nexus3.png)


<font color=red>@注意: 默认密码不是admin123,而是在容器中的/nexus-data/admin.password文件中</font>

## 2.1 找密码

```
# 进入服务目录
root@hui-X555LD ~ » cd /service

# 进入nexus服务容器中
root@hui-X555LD /service » docker-compose exec nexus bash

# 在容器中查看密码
bash-4.4$ cat /nexus-data/admin.password

# 密码
2f3ab5c2-ea5c-40b5-8677-1fce929687cb
```

登录之后，根据引导，可以设置新密码。

# 3. 使用nexus3

## 3.1 第一步:修改maven的配置文件
在maven的配置文件(settings.xml)中，servers 节点下添加以下代码

``` 
<servers>
  ...
  
  <!-- 个人私服开始节点 -->
      <server>
        <!-- 注意:pom.xml中的id要和这个一致！！ -->
        <id>self-nexus-releases</id>
        <username>admin</username>
        <password>123456</password>
      </server>
      <server>
        <id>self-nexus-snapshots</id>
        <username>admin</username>
        <password>123456</password>
      </server>
      <!-- 个人私服结束节点 -->
</servers>
```
## 3.2 第二步:修改项目pom.xml文件

修改项目中pom.xml文件，添加节点distributionManagement，代码如下:

```$xslt
<!--  个人私服配置 -开始节点 -->
<!--  id名称必须要与 maven settings.xml 中 Servers 配置的ID名称保持一致-->
<distributionManagement>
    <repository>
        <id>self-nexus-releases</id>
        <name>Nexus Release Repository</name>
        <!-- 这个地址是nexus3的服务地址-->
        <url>http://192.168.0.110:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
        <id>self-nexus-snapshots</id>
        <name>Nexus Snapshot Repository</name>
         <!-- 这个地址是nexus3的服务地址-->
        <url>http://192.168.0.110:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
<!--  个人私服配置 -结束节点-->
```

<font color=orange>@注意: repository节点和snapshotRepository节点下的id名称必须要与maven的配置文件(settings.xml)中的server下配置的id名称保持一致</font>