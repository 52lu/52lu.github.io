---
title: docker命令整理
date: 2018-08-13 10:33:29
tags:
 - docker
categories:
 - docker
---


## 1.基本信息查看
```
# 查看docker的版本号，包括客户端、服务端、依赖的Go等
$ docker version

# 查看系统(docker)层面信息，包括管理的images, containers数等
$ docker info 
```


## 2.镜像使用

### 2.1 搜索镜像
``` 
# 在docker index中搜索image
$ docker search <image> 
```
### 2.2 下载镜像
```
# 从docker registry server 中下拉image
$ docker pull <image>  
```
### 2.3 查看镜像 
```
$ docker images： # 列出images
$ docker images -a # 列出所有的images（包含历史）
$ docker rmi  <image ID>： # 删除一个或多个image
```
### 2.4 使用镜像创建容器 
```
$ docker run -i -t sauloal/ubuntu14.04

# 创建一个容器，让其中运行 bash 应用，退出后容器关闭
$ docker run -i -t sauloal/ubuntu14.04 /bin/bash
 
#创建一个名称centos_aways的容器，自动重启，--restart参数：always始终重启；on-failure退出状态非0时重启；默认为，no不重启
$ docker run -itd --name centos_aways --restart=always centos 
```

### 2.5 删除为none的镜像
Dockerfile 代码更新频繁，自然docker build构建同名镜像也频繁的很，产生了众多名为none的无用镜像。
分别执行以下三行可清除:

```
$ docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker stop

$ docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker rm

$ docker images|grep none|awk '{print $3 }'|xargs docker rmi

```

## 3.容器使用
### 3.1 查看容器
```
$ docker ps ：列出当前所有正在运行的container
$ docker ps -l ：列出最近一次启动的container
$ docker ps -a ：列出所有的container（包含历史，即运行过的container）
$ docker ps -q ：列出最近一次运行的container ID
```

### 3.2 启动容器
```
#：开启/停止/重启container
$ docker start/stop/restart <container> 

#：再次运行某个container （包括历史container）
$ docker start [container_id]
```

### 3.3 进入容器
```
$ docker exec -it [container_id] /bin/bash

#：映射 HOST 端口到容器，方便外部访问容器内服务，host_port 可以省略，省略表示把 container_port 映射到一个动态端口。
$ docker run -i -t -p <host_port:contain_port>
```

### 3.4 删除容器

```
#：删除一个或多个container
$ docker rm <container...> 

#：删除所有的container
$ docker rm `docker ps -a -q`

#：同上, 删除所有的container
$ docker ps -a -q | xargs docker rm

# 停止所有容器，删除所有容器
$ docker kill $(docker ps -q) 
$ docker rm $(docker ps -a -q)

```

### 3.5 查看容器IP
- 进入容器内部后
```
cat /etc/hosts
```
- 使用命令
```
docker inspect <container id> 
# 或 
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id
```
- 获取所有容器名称及其IP

> {% raw %}
$ docker inspect --format='{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
{% endraw %}


```
workdock_nginx_1 - 192.168.0.8172.31.0.3
workdock_php-fpm_1 - 192.168.0.7
workdock_workspace_1 - 192.168.0.6172.31.0.2
workdock_redis_1 - 192.168.0.5
workdock_mysql_1 - 192.168.0.2
workdock_mongo_1 - 192.168.0.4
workdock_docker-in-docker_1 - 192.168.0.3
```





