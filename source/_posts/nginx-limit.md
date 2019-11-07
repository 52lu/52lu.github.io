---
title: Nginx限流并发(防止CC、DDos攻击)
date: 2019-11-07 13:31:15
tags:
- 安全
categories:
 - nginx
---
# 1. 限流算法
## 1.1 漏桶算法
以固定速率从桶中流出水滴，以任意速率往桶中放入水滴，桶容量大小是不会发生改变的。  
 - 流入：以任意速率往桶中放入水滴。
 - 流出：以固定速率从桶中流出水滴。
 - 水滴：是唯一不重复的标识。

因为桶中的容量是固定的，如果流入水滴的速率>流出的水滴速率，桶中的水滴可能会溢出。那么溢出的水滴请求都是拒绝访问的，或者直接调用服务降级方法

## 1.2 令牌桶算法
- 令牌以固定速率产生，并缓存到令牌桶中；
- 令牌桶放满时，多余的令牌被丢弃；
- 请求要消耗等比例的令牌才能被处理；
- 令牌不够时，请求被缓存。

相比漏桶算法，令牌桶算法不同之处在于它不但有一只“桶”，还有个队列，这个桶是用来存放令牌的，队列才是用来存放请求的

从作用上来说，漏桶和令牌桶算法最明显的区别就是是否允许突发流量(burst)的处理，漏桶算法能够强行限制数据的实时传输（处理）速率，对突发流量不做额外处理；而令牌桶算法能够在限制数据的平均传输速率的同时允许某种程度的突发传输。

# 2. 限速使用
nginx可以通过limit_conn_zone 和limit_req_zone两个组件来对客户端访问目录和文件的访问频率和次数进行限制，另外还可以善用进行服务安全加固，两个模块都能够对客户端访问进行限制。能够对 [CC攻击](https://baike.baidu.com/item/CC%E6%94%BB%E5%87%BB)、[DDOS攻击](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E6%8B%92%E7%BB%9D%E6%9C%8D%E5%8A%A1%E6%94%BB%E5%87%BB/3802159?fromtitle=DDOS%E6%94%BB%E5%87%BB&fromid=177090)等此类进行有效的防御。

## 2.1 示例
```
http {   
    # 定义一个名称为one内存区域大小为10m,表示请求的速率是1秒10个请求。
    limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
    # 定义 按IP配置内存区域zone的大小为10m:
    limit_conn_zone $binary_remote_addr zone=perip_conn:10m;
    # 定义 按server配置一个连接 zone
    limit_conn_zone $server_name zone=perserver_conn:100m;
}

# 负载均衡配置
upstream server_list{
    server 192.168.40.11:8000 max_fails=3 fail_timeout=10s weight=50;
    server 192.168.40.12:8000 max_fails=3 fail_timeout=10s weight=100;
    server 192.168.40.13:8000 max_fails=3 fail_timeout=10s weight=100;
    keepalive 1000;
}
server {
    listen       8887;
    server_name  127.0.0.1;
    access_log  /var/log/nginx/sz_proxy_access.log  main;
    location / {
        # 使用one(与上面limit_req_zone，的name一致)区域配置限制，burst队列长度为100,nodelay:是否抛出503
        limit_req zone=one burst=100 nodelay;
        # 限制同一个IP同一时间来源的连接数:10个
        limit_conn perip_conn 10;
        # 限制同一个虚拟服务器同一时的总连接数
        limit_conn perserver_conn 2000;
        # 限制速度
        # limit_rate 1k;
        proxy_buffer_size 64k;
        proxy_buffers 32 32k;
        proxy_busy_buffers_size 128k;
        proxy_set_header Connection "Keep-Alive";
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_intercept_errors on;
        proxy_pass http://server_list/;
    }
    # 以上限制，超出后会报503，以下代码会把把503转成200，并返回固定的错误页面
    error_page   503 =200  /success.html;
    location = /success.html {
       root /data/web/test/www/;
       index error.html;
    }
}

```

## 2.2 示例解析

### 2.2.1 http 配置段

#### 1. limit_req_zone

**语法**
> limit_req_zone $binary_remote_addr zone=one:?m rate=?r/s;

```
# 限速配置: 设置一个名称为one内存区域大小为10m,表示请求的速率是1秒10个请求。
limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
```
 <!--- zone:one:10m 表示一个内存区域大小为10m,并且设定了名称为one-->
 <!--- rate=10r/s 表示请求的速率是1秒10个请求，当单位设置成60r/m时，并不能达到限速1分钟60次的效果，它等同于1r/s。-->
 <!--- $binary_remote_addr 表示远程的ip地址，当此nginx前方还存在代理时，需进行处理-->
 
- 第一个参数key: $binary_remote_addr 表示远程的ip地址，当此nginx前方还存在代理时，需进行处理。

- 第二个参数zone: 定义共享内存区来存储访问信息，1M能存储16000 IP地址的访问信息，10M可以存储16W个IP地址访问信息。zone=one:10m表示生成一个大小为10M，名字为one的内存区域
- 第三个参数rate : rate=1r/s表示允许相同标识的客户端的访问频次，这里限制的是每秒1次
> 当单位设置成60r/m时，并不能达到限速1分钟60次的效果，它等同于1r/s


#### 2. limit_conn_zone

**语法**
> imit_conn_zone $variable zone=name:size;

**参数说明:**
- $variable: 定义的键，要限流的维度,[$binary_remote_addr:同一客户端IP，$server_name:限制同一server最大并发数]
- size: 定义各个键共享内存空间大小
- name: zone名称


```
# 按IP配置内存区域zone的大小为10m:
limit_conn_zone $binary_remote_addr zone=perip_conn:10m;

# 按server配置一个连接 zone
limit_conn_zone $server_name zone=perserver_conn:100m;
```

### 2.2.2 server 配置段

#### 1. limit_req
```
limit_req zone=one burst=100 nodelay;
```
- zone=one : 设置使用哪个配置区域来做限制，与上面limit_req_zone 里的name对应
- burst=100 : 表示请求队列的长度
- nodelay : 表示不延时

#### 2. limit_conn
```
 # 限制同一个IP同一时间来源的连接数:10个
 limit_conn perip_conn 10;
 
 # 限制同一个虚拟服务器同一时的总连接数
 limit_conn perserver_conn 2000;
```
#### 3.limit_rate 限速

```
# 从下载指定的文件大小(1M)之后开始限速
limit_rate_after 1m;
 
# 限制请求资源
limit_rate 1k;
```
## 2.3 为什么使用$binary_remote_addr，而不使用$remote_addr
`原因是，两个变量的长度不一样。`
- $remote_addr变量的长度为7字节到15字节，而存储状态在32位平台中占用32字节或64字节，在64位平台中占用64字节。
- $binary_remote_addr变量的长度是固定的4字节，存储状态在32位平台中占用32字节或64字节，在64位平台中占用64字节。