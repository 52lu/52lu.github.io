---
title: Nginx-反向代理
date: 2017-04-17 12:32:56
tags:
 - nginx
categories:
 - 服务使用
---

#### 关键词匹配 
```
location ~ ^/TEST/ {
    rewrite     /TEST/(.*)$ /$1 break;
    proxy_next_upstream http_502 http_504 error timeout invalid_header;
    proxy_set_header Host  $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   	proxy_pass http://ip:port;
    expires     3d;
}
```

#### 域名转发
```
server {
   listen	 80;
   server_name 域名;
   location / {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header Host $http_host;
      proxy_set_header REMOTE-HOST $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass http://ip:port;
   }
}
```
#### 转发到多台
```
upstream baidunode {
   server xxx:port weight=10 max_fails=3  fail_timeout=30s;
   server xxx:port weight=10 max_fails=3  fail_timeout=30s;
}
location / {
   add_header Cache-Control no-cache;
   proxy_set_header   Host local.baidu.com;
   proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
   proxy_set_header   X-Real-IP        $remote_addr;
   proxy_pass         http://baidunode;
   proxy_connect_timeout 30s;
}
```