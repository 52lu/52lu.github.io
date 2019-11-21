---
title: Nginx配置调优
date: 2019-03-28 20:57:02
tags:
 - nginx
categories:
 - 服务使用
---

## 1. nginx.conf 配置文件
```
#运行的用户
user www-data;
pid /var/run/nginx.pid;

#定义了nginx对外提供web服务时的worder进程数，CPU核的数量、存储数据的硬盘数量及负载模式
worker_processes auto; 

#更改worker进程的最大打开文件数限制,比'ulimit -a'多。解决"too many open files"
worker_rlimit_nofile 100000; 
events {
    #worker进程同时打开的最大连接数
    worker_connections 65536; 
    #当收到一个新连接通知后接受尽可能多的连接
    multi_accept on; 
    #设置用于复用客户端线程的轮询方法,epoll需要linux2.6+
    use epoll; 
}
http {
    access_log off; #设置nginx是否将存储访问日志。关闭这个选项可以让读取磁盘IO操作更快(aka,YOLO)
    error_log /var/log/nginx/error.log crit; #告诉nginx只能记录严重的错误
    
  #HTTP和TCP优化
    keepalive_timeout 10; #给客户端分配keep-alive链接超时时间。服务器将在这个超时时间过后关闭链接
    keepalive_requests 1024;#限制了一个 HTTP 长连接最多可以处理完成的最大请求数, 默认是 100。当连接处理完成的请求数达到最大请求数后，将关闭连接。
    tcp_nopush on; #在一个数据包里发送所有头文件，而不一个接一个的发送
    tcp_nodelay on; #nginx不要缓存数据，而是一段一段的发送--当需要及时发送数据时，就应该给应用设置这个属性，这样发送一小块数据信息时就不能立即得到返回值。
    
  #Buffers size优化
    client_body_buffer_size  128k;#处理客户端缓冲区大小。 大多数客户端缓冲区都来自POST方法表单提交。128k通常是此设置的不错选择。
    client_max_body_size  10m;#置最大body缓冲区大小。如果请求中的大小超过配置的值，则会将413（请求实体过大）错误返回给客户端。 不过浏览器无法正确显示413错误。 将大小设置为0将禁用对客户机请求正文大小的检查
    client_header_buffer_size    1k; #处理客户端头大小。 默认情况下，1k通常是一个合理的选择。
    large_client_header_buffers  4 4k; #设置客户端头的缓冲区的最大数量和大小。 4个报头与4k缓冲区应该足够了。
    output_buffers  1 32k;#置用于从磁盘读取响应的缓冲区的数量和大小。 如果可能，客户端数据的传输将被推迟，直到Nginx至少具有要发送的数据的字节数的集合大小。零值禁止推迟数据传输。
    postpone_output  1460;#指定Nginx发送给客户端最小的数值，如果可能的话，没有数据会发送，直到达到此值
    
   #超时
    client_header_timeout 10; #设置请求头超时时间，建议低
    client_body_timeout 10; #请求体(各自)的超时时间
    reset_timedout_connection on; #关闭不响应的客户端连接。这将会释放那个客户端所占有的内存空间
    send_timeout 10; #指定客户端的响应超时时间
    
  #静态资源服务
    open_file_cache max=100000 inactive=20s; #打开缓存的同时也指定了缓存最大数目，以及缓存的时间
    open_file_cache_valid 30s; #在open_file_cache中指定检测正确信息的间隔时间
    open_file_cache_min_uses 2; #定义了open_file_cache中指令参数不活动时间期间里最小的文件数
    open_file_cache_errors on; #指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件
    
  #Gzip压缩内容
    gzip on; #开启gzip压缩
    gzip_disable "msie6"; #低版本兼容
    gzip_proxied any; #允许或者禁止压缩基于请求和响应的响应流。我们设置为any，意味着将会压缩所有的请求
    gzip_static on; #告诉nginx在压缩资源之前，先查找是否有预先gzip处理过的资源
    gzip_min_length 1000; #设置对数据启用压缩的最少字节数
    gzip_comp_level 6; #压缩级别,1-10越高越消耗CPU，但是压缩越好
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript; #设置需要压缩的数据格式
  
  #其他优化
    limit_conn_zone $binary_remote_addr zone=addr:5m; #设置用于保存各种key（比如当前连接数）的共享内存的参数。5m就是5兆字节，这个值应该被设置的足够大以存储（32K*5）32byte状态或者（16K*5）64byte状态。
    limit_conn addr 100; #给定的key设置最大连接数。这里key是addr，我们设置的值是100，也就是说我们允许每一个IP地址最多同时打开有100个连接。
    server_tokens off; #关闭nginx版本号
    sendfile on; #sendfile()可以在磁盘和TCP socket之间互相拷贝数据(或任意两个文件描述符)。Pre-sendfile是传送数据之前在用户空间申请数据缓冲区。之后用read()将数据从文件拷贝到这个缓冲区，write()将缓冲区数据写入网络。sendfile()是立即将数据从磁盘读到OS缓存。因为这种拷贝是在内核完成的，sendfile()要比组合read()和write()以及打开关闭丢弃缓冲更加有效(更多有关于sendfile)
    
    default_type text/html; #设置文件默认MIME-type
    include /etc/nginx/mime.types; #只是一个在当前文件中包含另一个文件内容的指令。
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

## 2.nginx.pid文件
### 2.1 什么是 var/run/nginx.pid文件?
<!--more-->
 var/run这个目录包含描述系统启动以来系统信息的数据。此文件夹下的文件必须在启动过程初期清除（删除或归零）。程序可以在/var/run下有自己的子文件夹。原先放在/etc下的进程标识（PID）文件必须放在/var/run里面。PID文件的命名惯例是<program-name>.pid。所以，nginx的PID文件名为/var/run/nginx.pid。

`nginx.pid存放的是nginx的master进程的进程号。`
### 2.2 nginx启动报错找不到nginx.pid
`nginx: [error] open() "/usr/local/nginx/logs/nginx.pid" failed (2: No such file or directory)`
```
[root@localhost nginx]# nginx -c /usr/local/nginx/conf/nginx.conf
```
## 3.Worker Processess 说明
`通常来说设置一个cpu核心对应一个worker processer，最多不超过4个，提高worker process的值是为了提高计算能力，但一般在越到cpu瓶颈前，你会遇到别的瓶颈(如网络问题)只有当你要处理大量静态文件的磁盘I/O时，worker进程是单线程的，所以这个读取文件的阻塞IO会降低CPU的处理速度，这是可以增加worker进程数量，其它情况是不需要的。`
```
//一般设置为:auto
# worker_processes auto;

//如果你想手动设置，你可以使用以下命令行查找服务器的cpu线程数。
# grep ^processor /proc/cpuinfo | wc -l
```

## 4.Worker进程连接数优化(worker_connections)
`worker_connections选项设置每个worker进程一次可以处理的最大连接数。 默认情况下，worker连接限制为512，但系统可以处理比这值更多的连接。
适当的值可以通过测试得出。`

> 系统的核心限制也可以通过使用ulimit找到

```
# ulimit -n
```

## 5.HTTP和TCP优化

### 5.1 Keep Alive:减少浏览器重新连接的次数
- keepalive_timeout和keepalive_requests 控制保持活动设置。
- sendfile 优化Nginx提供文件系统的静态文件服务的性能，如logo。
- tcp_nodelay 允许Nginx使TCP发送多个缓冲区作为单独的数据包。
- tcp_nopush 通过激活TCP堆栈中的TCP_CORK选项来优化一次向下游发送的数据量。TCP_CORK阻塞数据，直到分组到达MSS，这等于MTU减去IP报头的40或60字节。

### 5.2 服务处于TIME_WAIT 状态的 TCP 连接数异常的原因
> keepalive_requests 参数限制了一个 HTTP 长连接最多可以处理完成的最大请求数, 默认是 100。当连接处理完成的请求数达到最大请求数后，将关闭连接

`而我并没有配置 keepalive_requests，所以，就是使用的默认数 100，即一个长连接只能处理一百个请求，然后 Nginx 就就会主动关闭连接，使大量连接处于 TIME_WAIT 状态`

## 6. Buffers size优化
`调整缓冲区大小可能是有利的。 如果缓冲区大小太低，那么Nginx将写入一个临时文件。 这将导致过多的磁盘I/O。`

- client_body_buffer_size 处理客户端缓冲区大小。 大多数客户端缓冲区都来自POST方法表单提交。 128k通常是此设置的不错选择。
- client_max_body_size 设置最大body缓冲区大小。 如果请求中的大小超过配置的值，则会将413（请求实体过大）错误返回给客户端。 不过浏览器无法正确显示413错误。 将大小设置为0将禁用对客户机请求正文大小的检查。
- client_header_buffer_size 处理客户端头大小。 默认情况下，1k通常是一个合理的选择。
- large_client_header_buffers 设置客户端头的缓冲区的最大数量和大小。 4个报头与4k缓冲区应该足够了。
- output_buffers 设置用于从磁盘读取响应的缓冲区的数量和大小。 如果可能，客户端数据的传输将被推迟，直到Nginx至少具有要发送的数据的字节数的集合大小。 零值禁止推迟数据传输。

## 7. 超时设置
`超时设置还可以大幅提高性能。`
- client_body_timeout 指令设置服务器等待客户端完成发送body的最大时间。
- client_header_timeout 指令设置服务器等待客户端完成发送header的最大时间。
- sent_timeout 指定对客户端的响应超时。 此超时不适用于整个传输，而是仅应用于两个后续的客户端读取操作之间。 因此，如果客户端没有读取任何数据这段时间，那么Nginx关闭连接。

## 8.静态资源服务
`如果您的网站提供静态资源（如CSS/JavaScript/images），Nginx可以缓存这些文件很短的时间。`
```
#告诉Nginx缓存1000个文件30秒，不包括在20秒内未被访问的任何文件
open_file_cache max=100000 inactive=20s; 
#在open_file_cache中指定检测正确信息的间隔时间
open_file_cache_valid 30s; 
#定义了open_file_cache中指令参数不活动时间期间里最小的文件数
open_file_cache_min_uses 2; 
#指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件
open_file_cache_errors on; 
```
>还可以缓存特定位置的资源,缓存文件很长时间是有好处的。如放在具体某个服务配置中..
```
location ~* .(woff|eot|ttf|svg|mp4|webm|jpg|jpeg|png|gif|ico|css|js)$ {
    expires 365d;
}
```
## 9.Gzip压缩内容
```
#开启gzip压缩
gzip on; 
#低版本兼容
gzip_disable "msie6"; 
#允许或者禁止压缩基于请求和响应的响应流。我们设置为any，意味着将会压缩所有的请求
gzip_proxied any; 
#告诉nginx在压缩资源之前，先查找是否有预先gzip处理过的资源
gzip_static on; 
#设置对数据启用压缩的最少字节数
gzip_min_length 1000; 
#压缩级别,1-10越高越消耗CPU，但是压缩越好
gzip_comp_level 6; 
#设置需要压缩的数据格式
gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript; 
```