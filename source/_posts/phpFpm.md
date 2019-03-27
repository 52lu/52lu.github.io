---
title: php-fpm配置详情及优化
date: 2019-03-10 16:36:05
tags: 
 - php-fpm
categories:
 - php
---
>PHP-FPM是一个PHPFastCGI管理器，是只用于PHP的。

## 配置文件详情注释
<!--more-->
```
pid = run/php-fpm.pid
#pid设置，默认在安装目录中的var/run/php-fpm.pid，建议开启

error_log = log/php-fpm.log
#错误日志，默认在安装目录中的var/log/php-fpm.log

log_level = notice
#错误级别. 可用级别为: alert（必须立即处理）, error（错误情况）, warning（警告情况）, notice（一般重要信息）, debug（调试信息）. 默认: notice.

emergency_restart_threshold = 60
emergency_restart_interval = 60s
#表示在emergency_restart_interval所设值内出现SIGSEGV或者SIGBUS错误的php-cgi进程数如果超过 emergency_restart_threshold个，php-fpm就会优雅重启。这两个选项一般保持默认值。

process_control_timeout = 0
#设置子进程接受主进程复用信号的超时时间. 可用单位: s(秒), m(分), h(小时), 或者 d(天) 默认单位: s(秒). 默认值: 0.

daemonize = yes
#后台执行fpm,默认值为yes，如果为了调试可以改为no。在FPM中，可以使用不同的设置来运行多个进程池。 这些设置可以针对每个进程池单独设置。

listen = 127.0.0.1:9000
#fpm监听端口，即nginx中php处理的地址，一般默认值即可。可用格式为: 'ip:port', 'port', '/path/to/unix/socket'. 每个进程池都需要设置.

listen.backlog = -1
#backlog数，-1表示无限制，由操作系统决定，此行注释掉就行。backlog含义参考：http://www.3gyou.cc/?p=41

listen.allowed_clients = 127.0.0.1
#允许访问FastCGI进程的IP，设置any为不限制IP，如果要设置其他主机的nginx也能访问这台FPM进程，listen处要设置成本地可被访问的IP。默认值是any。每个地址是用逗号分隔. 如果没有设置或者为空，则允许任何服务器请求连接

listen.owner = www
listen.group = www
listen.mode = 0666
#unix socket设置选项，如果使用tcp方式访问，这里注释即可。

user = www
group = www
#启动进程的帐户和组

pm = dynamic #对于专用服务器，pm可以设置为static。
#如何控制子进程，选项有static和dynamic。如果选择static，则由pm.max_children指定固定的子进程数。如果选择dynamic，则由下开参数决定：
pm.max_children #，子进程最大数
pm.start_servers #，启动时的进程数
pm.min_spare_servers #，保证空闲进程数最小值，如果空闲进程小于此值，则创建新的子进程
pm.max_spare_servers #，保证空闲进程数最大值，如果空闲进程大于此值，此进行清理

pm.max_requests = 1000
#设置每个子进程重生之前服务的请求数. 对于可能存在内存泄漏的第三方模块来说是非常有用的. 如果设置为 '0' 则一直接受请求. 等同于 PHP_FCGI_MAX_REQUESTS 环境变量. 默认值: 0.

pm.status_path = /status
#FPM状态页面的网址. 如果没有设置, 则无法访问状态页面. 默认值: none. munin监控会使用到

ping.path = /ping
#FPM监控页面的ping网址. 如果没有设置, 则无法访问ping页面. 该页面用于外部检测FPM是否存活并且可以响应请求. 请注意必须以斜线开头 (/)。

ping.response = pong
#用于定义ping请求的返回相应. 返回为 HTTP 200 的 text/plain 格式文本. 默认值: pong.

request_terminate_timeout = 0
#设置单个请求的超时中止时间. 该选项可能会对php.ini设置中的'max_execution_time'因为某些特殊原因没有中止运行的脚本有用. 设置为 '0' 表示 'Off'.当经常出现502错误时可以尝试更改此选项。

request_slowlog_timeout = 10s
#当一个请求该设置的超时时间后，就会将对应的PHP调用堆栈信息完整写入到慢日志中. 设置为 '0' 表示 'Off'

slowlog = log/$pool.log.slow
#慢请求的记录日志,配合request_slowlog_timeout使用

rlimit_files = 1024
#设置文件打开描述符的rlimit限制. 默认值: 系统定义值默认可打开句柄是1024，可使用 ulimit -n查看，ulimit -n 2048修改。

rlimit_core = 0
#设置核心rlimit最大限制值. 可用值: 'unlimited' 、0或者正整数. 默认值: 系统定义值.

chroot =
#启动时的Chroot目录. 所定义的目录需要是绝对路径. 如果没有设置, 则chroot不被使用.

chdir =
#设置启动目录，启动时会自动Chdir到该目录. 所定义的目录需要是绝对路径. 默认值: 当前目录，或者/目录（chroot时）

catch_workers_output = yes
#重定向运行过程中的stdout和stderr到主要的错误日志文件中. 如果没有设置, stdout 和 stderr 将会根据FastCGI的规则被重定向到 /dev/null . 默认值: 空.
```



## 主要参数介绍
- ### pm (进程管理)
`管理进程方式: pm = static/dynamic`

1. static(静态) ：表示在fpm运行时直接fork出pm.max_chindren个worker进程
2. dynamic(动态)：表示运行时fork出start_servers个进程，随着负载的情况，动态的调整，最多不超过max_children个进程

> 一般推荐用static，优点是不用动态的判断负载情况，提升性能，缺点是多占用些系统内存资源。

- ### max_children(子进程最大数)
1. 这个值原则上是越大越好，php-cgi的进程多了就会处理的很快，排队的请求就会很少。

2. 设置'max_children'需要根据服务器的性能进行设定

3. 一般来说一台服务器正常情况下每一个php-cgi所耗费的内存在20M左右

4. 假设“max_children”设置成100个，20M*100=2000M

5. 在峰值的时候所有PHP-CGI所耗内存在2000M以内。

6. 假设'max_children'设置的较小，比如5-10个，那么php-cgi就会“很累”，处理速度也很慢，等待的时间也较长。

7. 如果长时间没有得到处理的请求就会出现504 Gateway Time-out这个错误，而正在处理的很累的那几个php-cgi如果遇到了问题就会出现502 Bad gateway这个错误。

- ### start_servers(启动时的进程数)
1. pm.start_servers的默认值为2。
> 其在php-fpm中给的计算方式也为：{（cpu空闲时等待连接的php的最小子进程数） + （cpu空闲时等待连接的php的最大子进程数 - cpu空闲时等待连接的php的最小子进程数）/ 2} 用配置表示就是：min_spare_servers + (max_spare_servers - min_spare_servers) / 2；
2. 一般而言，设置成10-20之间的数据足够满足需求了

- ### max_requests(最大请求数)

>最大处理请求数是指一个php-fpm的worker进程在处理多少个请求后就终止掉，master进程会重新spawn一个新的。这个配置的主要目的是避免php解释器或程序引用的第三方库造成的内存泄露。

- 出现502的原因？

`502，是后端PHP-FPM不可用造成的，间歇性的502一般认为是由于PHP-FPM进程重启造成的`

- 但是为什么要重启进程呢？

`如果不定期重启 PHP-CGI 进程，势必造成内存使用量不断增长（比如第三方库有问题等）。因此 PHP-FPM 作为 PHP-CGI 的管理器，提供了这么一项监控功能，对请求达到指定次数的 PHP-CGI 进程进行重启，保证内存使用量不增长`

> <front color=red>也正是因为这个机制，在高并发中，经常导致502错误，</front>

- ### request_terminate_timeout(最长执行时间)
`设置单个请求的超时中止时间。该选项可能会对 php.ini 设置中的 'max_execution_time' 因为某些特殊原因没有中止运行的脚本有用。设置为 '0' 表示 'Off'。`

>这两项都是用来配置一个PHP脚本的最大执行时间的。当超过这个时间时，PHP-FPM不只会终止脚本的执行，还会终止执行脚本的Worker进程,Nginx会发现与自己通信的连接断掉了，就会返回给客户端502错误


## php-fpm 调优
- 尽量少安装PHP模块
- 的PHPFastCGI子进程数（max_children）调到适应大小
- 使用socket连接FastCGI，linux操作系统可以放在 /dev/shm中
> 注：在php-fpm.cnf里设置<value name=”listen_address”>/tmp/nginx.socket</value>就可以通过socket连接FastCGI了，/dev/shm是内存文件系统，放在内存中肯定会快了.记得这时也要在nginx里的配置里进行修改，保持一致．
``` 
location ~ \.php${
   #将Nginx与FastCGI的通信方式由TCP改为Unix Socket。TCP在高并发访问下比Unix Socket稳定，但Unix Socket速度要比TCP快。
   fastcgi_pass  unix:/tmp/php-cgi.sock;
   #fastcgi_pass  127.0.0.1:9000;
   fastcgi_index index.php;
   include fcgi.conf;
}
```

- 以root身份执行以下命令，调高linux内核打开文件数量
``` 
 echo 'ulimit -HSn 65536' >> /etc/profile
 echo 'ulimit -HSn 65536' >> /etc/rc.local
 source /etc/profile 
```

- 增加 PHP-FPM 打开文件描述符的限制:
> 把php-fpm.conf文件中的rlimit_files值1024改为4096或者更高，然后<front color='red'>重启 PHP-FPM</front>

- 使用php代码加速器，例如 eAccelerator, XCache.在linux平台上可以把`cache_dir`指向 /dev/shm