---
title: 命令-curl
date: 2015-07-12 11:55:26
tags:
  - linux
categories:
  - 命令使用
---

# 1. 表单提交
## 1.1 GET数据提交
```
$ curl https://cn.bing.com/search?q=go
```
## 1.2 POST数据提交

- 不设置header提交

```
curl -d "user=admin&passwd=12345678" http://127.0.0.1:8080/login
```
- 设置header提交

```
curl -H "Content-Type:application/json" -X POST -d '{"user": "admin", "passwd":"12345678"}' http://127.0.0.1:8000/login

```



# 2.使用代理请求
> *linux curl命令可以使用下面参数设置http(s)代理、socks代理，已经设置它们的用户名、密码以及认证方式*

参数 | 用法
---|---
 -x host:port<br/> -x [protocol://[user:pwd@]host[:port]<br/>--proxy [protocol://[user:pwd@]host[:port]<br/>| 使用HTTP代理访问；如果未指定端口，默认使用8080端口;<br/>protocol默认为http_proxy，其他可能的值包括：<br/>http_proxy、HTTPS_PROXY、socks4、socks4a、socks5；<br/>如：<br/>--proxy 8.8.8.8:8080；<br/>-x "http_proxy://aiezu:123@aiezu.com:80"
--socks4 <host[:port]><br/>--socks4a <host[:port]><br/>--socks5 <host[:port]><br/> | 使用SOCKS4代理；<br/>使用SOCKS4A代理；<br/>使用SOCKS5代理；<br/>此参数会覆盖“-x”参数；<br/>
--proxy-anyauth<br/>--proxy-basic<br/>--proxy-diges<br/>--proxy-negotiate<br/>--proxy-ntlm<br/> | 代理认证方式，参考：<br/> --anyauth <br/> --basic <br/> --diges <br/> --negotiate <br/> --ntlm <br/>
-U <user:password> <br/>--proxy-user <user:password> | 设置代理的用户名和密码；


## 2.1 http/https代理

### 1. 指定http代理IP和端口
```
# 指定http代理IP和端口
curl -x 113.185.19.192:80 http://aiezu.com/test.php
curl --proxy 113.185.19.192:80 http://aiezu.com/test.php
```
### 2.指定为http代理
```
#指定为http代理
curl -x http_proxy://113.185.19.192:80 http://aiezu.com/test.php
```
### 3.指定为https代理
```
curl -x HTTPS_PROXY://113.185.19.192:80 http://aiezu.com/test.php
```
### 4.指定代理用户名和密码，basic认证方式
```
curl -x aiezu:123456@113.185.19.192:80 http://aiezu.com/test.php
curl -x 113.185.19.192:80 -U aiezu:123456 http://aiezu.com/test.php
curl -x 113.185.19.192:80 --proxy-user aiezu:123456 http://aiezu.com/test.php
```
### 5.指定代理用户名和密码，ntlm认证方式
```
#指定代理用户名和密码，ntlm认证方式
curl -x 113.185.19.192:80 -U aiezu:123456 --proxy-ntlm http://aiezu.com/test.php
```
### 6.指定代理协议、用户名和密码，basic认证方式
```
curl -x http_proxy://aiezu:123456@113.185.19.192:80 http://aiezu.com/test.php
```

## 2.2.socks代理：
### 1. 使用socks4代理，无需认证方式
```
curl --socks4 122.192.32.76:7280 http://aiezu.com/test.php
curl -x socks4://122.192.32.76:7280 http://aiezu.com/test.php
```
### 2.使用socks4代理，无需认证方式
```
curl --socks4a 122.192.32.76:7280 http://aiezu.com/test.php
curl -x socks4a://122.192.32.76:7280 http://aiezu.com/test.php
```
### 3.使用socks5代理，basic认证方式
```
curl --socks5 122.192.32.76:7280 -U aiezu:123456 http://aiezu.com/test.php
curl -x socks5://aiezu:123456@122.192.32.76:7280 http://aiezu.com/test.php
```
### 4.使用socks5代理，basic认证方式，ntlm认证方式
```
curl -x socks5://aiezu:123456@122.192.32.76:7280 --proxy-ntlm http://aiezu.com/test.php
```