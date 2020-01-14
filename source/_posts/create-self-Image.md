---
title: Docker-创建自己的镜像
date: 2019-05-21 16:27:11
tags:
 - docker
categories:
 - 容器管理
---

### 1.创建一个简单的Node.js 应用镜像

#### 1.1 创建一个本地目录
 ```
 makedir helloword
 ```
#### 1.2 编写应用代码server.js,代码保存到 "helloword/"文件下
 ```
var http = require('http');
var handleRequest = function(request, response) {
  console.log('Received request for URL: ' + request.url);
  response.writeHead(200);
  response.end('Hello World!');
};
var www = http.createServer(handleRequest);
www.listen(8080);
 ```
 
#### 1.3 编写Dockerfile文件
 ```
 FROM node:4.4
 EXPOSE 8080
 COPY server.js .
 CMD node server.js
 ```
#### 1.4  创建新镜像，给它起一个名字

> 使用命令: docker build [OPTIONS] PATH | URL | -
`--tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。`

 ```
➜ docker build -t liuqh/hellonde:v1 .
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM node:4.4
4.4: Pulling from library/node
357ea8c3d80b: Pull complete
52befadefd24: Pull complete
3c0732d5313c: Pull complete
ceb711c7e301: Pull complete
868b1d0e2aad: Pull complete
3a438db159a5: Pull complete
Digest: sha256:7b657ccf24be2c8bab969b215e6853bc87a0d2fbe0896d5305cc87122f5360d0
Status: Downloaded newer image for node:4.4
 ---> 93b396996a16
Step 2/4 : EXPOSE 8080
 ---> Running in a7798374f671
Removing intermediate container a7798374f671
 ---> 0c394b4854fa
Step 3/4 : COPY server.js .
 ---> cb0dd1a3ffed
Step 4/4 : CMD node server.js
 ---> Running in 7d1ec9e39d79
Removing intermediate container 7d1ec9e39d79
 ---> db149c04d3fa
Successfully built db149c04d3fa
Successfully tagged liuqh/hellonde:v1
 
 ```
 ><span style="color:red">注意命令后面的圆点</span>
 
#### 1.5 查看本地镜像，会发现自己建议的镜像
 ```
➜ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
liuqh/hellonde         v1                  db149c04d3fa        12 seconds ago      648MB
laradock_nginx         latest              fb8945ae0cb8        11 days ago         27.6MB
 ```

#### 1.6 运行容器
```
➜  docker run -d -p 8080:8080 --name hello-word liuqh/hellonde:v1
f18a619dafb3e333cd3592ce5e8e7b373d622c81fe968558307970f22cb1a92a
```

#### 1.7 验证
在浏览器输入 http://127.0.0.1:8080



###  2.镜像上传至[dockerHub](https://hub.docker.com)
`前提是有自己的帐号，没有请去注册。`

#### 2.1 将容器commit 成镜像

>使用命令 docker commit <exiting-Container> <hub-user>/<repo-name>[:<tag>] 

```
➜ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                                            NAMES
f18a619dafb3        liuqh/hellonde:v1   "/bin/sh -c 'node se…"   3 seconds ago       Up 2 seconds        0.0.0.0:8080->8080/tcp                                           hello-word

➜ docker commit f18a619dafb3 liuqinghui/helloword
sha256:efb1301fb0fdd607b4e0ff2c34e3d36c890c665a14abef1bf82316ec05938491
```

> `如果是已经存在的镜像则使用:docker tag <existing-image> <hub-user>/<repo-name>[:<tag>]`

这里的<tag>不指定就是latest。

#### 2.2 push 镜像到docker hub的仓库

```
# 登录 docker hub
➜ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: liuqinghui
Password:
Login Succeeded

# docker push 镜像到docker hub 的仓库
➜ docker push liuqinghui/helloword:v1
The push refers to repository [docker.io/liuqinghui/helloword]
99393065d499: Pushed
20a6f9d228c0: Mounted from library/node
80c332ac5101: Mounted from library/node
04dc8c446a38: Mounted from library/node
1050aff7cfff: Mounted from library/node
66d8e5ee400c: Mounted from library/node
2f71b45e4e25: Mounted from library/node
v1: digest: sha256:b567d7fbcfe5d1987d2bd6fd8f8633f11ca32c666868cb6366a5d0ae3380a9fe size: 1794
```