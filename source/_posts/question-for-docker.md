---
title: docker 使用中遇到的问题
date: 2018-05-03 15:54:34
tags:
 - docker
categories:
 - bug
---



##### 问题1>E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?

具体报错：
```
E: Fetched 1837 kB in 21s (84.9 kB/s)
Failed to fetch http://security.debian.org/pool/updates/main/p/php5/php5-common_5.6.33+dfsg-0+deb8u1_amd64.deb  404  Not Found

E: Failed to fetch http://security.debian.org/pool/updates/main/p/php5/php5-cli_5.6.33+dfsg-0+deb8u1_amd64.deb  404  Not Found

E: Failed to fetch http://security.debian.org/pool/updates/main/p/php5/php-pear_5.6.33+dfsg-0+deb8u1_all.deb  404  Not Found

E: Failed to fetch http://security.debian.org/pool/updates/main/p/php5/php5-readline_5.6.33+dfsg-0+deb8u1_amd64.deb  404  Not Found

E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
ERROR: Service 'php-fpm' failed to build: The command '/bin/sh -c if [ ${INSTALL_SOAP} = true ]; then     rm /etc/apt/preferences.d/no-debian-php      && apt-get -y install libxml2-dev php-soap      && docker-php-ext-install soap ;fi' returned a non-zero code: 100

```
<!--more-->
> 在相应的扩展中添加「 apt-get update 」如下：

<span style="color:green">解决方案:</span>
```
ARG INSTALL_SOAP=false

RUN if [ ${INSTALL_SOAP} = true ]; then \
    rm /etc/apt/preferences.d/no-debian-php  \
    && apt-get update -y \
    && apt-get -y install libxml2-dev php-soap  \
    && docker-php-ext-install soap \
;fi
```

<!--##### 问题2> E: Some index files failed to download. They have been ignored, or old ones used instead-->



##### 问题2>ERROR: Service 'redis' failed to build: OCI runtime create failed: container_linux.go:344: starting container process caused "process_linux.go:293: copying bootstrap data to pipe caused \"write init-p: broken pipe\"": unknown


<span style="color:green">解决方案:</span>
- 通过容器降级
```
sudo yum downgrade -y containerd.io-1.2.2-3.el7
```



##### 问题3> Unable to find expected entry 'main/binary-amd64/Packages'

**Unable to locate package**
apt 需要换成国内源

```
Step 94/98 : RUN apt-get update && apt-get install -y software-properties-common && add-apt-repository -y ppa:alex-p/tesseract-ocr
 ---> Running in 252ccd15262a
Get:1 http://security.debian.org jessie/updates InRelease [44.9 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [7340 B]
Get:3 http://deb.debian.org jessie Release.gpg [2420 B]
Get:4 http://deb.debian.org jessie Release [148 kB]
Get:5 http://security.debian.org jessie/updates/main amd64 Packages [832 kB]
Get:6 http://deb.debian.org jessie/main amd64 Packages [9098 kB]
Get:7 http://security.debian.org jessie/updates/main amd64 Packages [832 kB]

Fetched 10.1 MB in 10min 9s (16.6 kB/s)
W: Failed to fetch http://deb.debian.org/debian/dists/jessie-updates/InRelease  Unable to find expected entry 'main/binary-amd64/Packages' in Release file (Wrong sources.list entry or malformed file)

E: Some index files failed to download. They have been ignored, or old ones used instead.
ERROR: Service 'php-fpm' failed to build: The command '/bin/sh -c apt-get update && apt-get install -y software-properties-common && add-apt-repository -y ppa:alex-p/tesseract-ocr' returned a non-zero code: 100

```
<span style="color:green">解决方案:</span>
在对应的命令行前加上：
```
RUN printf " #deb包\n deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse\n deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse\n deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse\n deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse\n ##测试版源\n deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse\n # 源码\n deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse\n deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse\n deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse\n deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse\n ##测试版源\n deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse\n" > /etc/apt/sources.list
```

##### 问题4> docker: Error response from daemon: Cannot link toto /laradock_mysql_1, as it does not belong to the default network
- 查看网络
```
NETWORK ID          NAME                               DRIVER              SCOPE
c8dfb8a44422        bridge                             bridge              local
0fe587efe71f        chaincode-docker-devmode_default   bridge              local
3d3c9e4de651        e2e_default                        bridge              local
dce17f3c06ca        ethereum-docker_default            bridge              local
0eca3f311ce7        host                               host                local
57912953f3e5        laradock_backend                   bridge              local
35be8b9a0f70        laradock_default                   bridge              local
d3e733ccb976        laradock_frontend                  bridge              local
d67e463037d2        none                               null                local
```
<span style="color:red">原因分析:</span>
造成问题的原因是 Docker-Compose命名约定造成。
>使用docker-compose时，您的网络名称是根据docker-compose“项目名称”决定的，该项目名称基于其所在目录的名称。您可以使用-project-name标志覆盖项目名称或 COMPOSE_PROJECT_NAME环境变量。 我的docker-compose.yml文件位于一个名为dock的目录中，这就是为什么docker-compose在创建正在运行的容器实例时选择了dock_default！

<span style="color:green">解决方案:</span>：run 命令后增加--net参数，根据docker-compose 网络


##### 问题5 > Centos7 Failed to get D-Bus connection: Operation not permitted

<span style="color:red">原因分析:</span>
>大约在0.6版，privileged被引入docker。
使用该参数，container内的root拥有真正的root权限。
否则，container内的root只是外部的一个普通用户权限

<span style="color:green">解决方案:</span>

```
 # docker run -d --name centos7 --privileged=true centos:7 /usr/sbin/init
```