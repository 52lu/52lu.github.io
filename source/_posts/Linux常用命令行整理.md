---
title: Linux常用命令行整理
date: 2015-06-19 17:24:14
tags:
  - linux
categories:
  - linux
---


## 关闭服务

```
☐ 根据端口号进行关闭

kill -9 $(netstat -nlp | grep :9501 | awk '{print $7}' | awk -F"/" '{ print $1 }')

☐ 根据服务名称进行关闭

kill -9 $(ps aux|grep swoole|grep -v grep|awk '{print $2}')
```
## 查找
```
☐ grep
grep -i pattern files ：不区分大小写地搜索。默认情况区分大小写，
grep -l pattern files ：只列出匹配的文件名， 
grep -L pattern files ：列出不匹配的文件名， 
grep -w pattern files ：只匹配整个单词，而不是字符串的一部分（如匹配‘magic’，而不是‘magical’）， 
grep -C number pattern files ：匹配的上下文分别显示[number]行，
grep pattern1 | pattern2 files ：显示匹配 pattern1 或 pattern2 的行，
grep pattern1 files | grep pattern2 ：显示既匹配 pattern1 又匹配 pattern2 的行。 
这里还有些用于搜索的特殊符号：
​
\< 和 \> 分别标注单词的开始与结尾。
​
例如： 
grep man * 会匹配 ‘Batman’、‘manic’、‘man’等， 
grep '\<man' * 匹配‘manic’和‘man’，但不是‘Batman’，
grep '\<man\>' 只匹配‘man’，而不是‘Batman’或‘manic’等其他的字符串。 
'^'：指匹配的字符串在行首， 
'$'：指匹配的字符串在行尾•

☐ find

1.自动删除n天前日志
     find 对应目录 -mtime +天数 -name "文件名" -exec rm -rf {} \;

2.找到大于指定大小的文件：

 find / -type f -size +10G
 
# find / -path "/media/xww" -type f -size +10G
```
## 系统
 
```
# uname -a               # 查看内核/操作系统/CPU信息
# head -n 1 /etc/issue   # 查看操作系统版本
# cat /proc/cpuinfo      # 查看CPU信息
# hostname               # 查看计算机名
# lspci -tv              # 列出所有PCI设备
# lsusb -tv              # 列出所有USB设备
# lsmod                  # 列出加载的内核模块
# env                    # 查看环境变量

```
## 资源
```
 
# free -m                # 查看内存使用量和交换区使用量
# df -h                  # 查看各分区使用情况
# du -sh <目录名>        # 查看指定目录的大小
# grep MemTotal /proc/meminfo   # 查看内存总量
# grep MemFree /proc/meminfo    # 查看空闲内存量
# uptime                 # 查看系统运行时间、用户数、负载
# cat /proc/loadavg      # 查看系统负载
# du -s /usr/* | sort -rn #这是按字节排序
# du -sh /usr/* | sort -rn #这是按兆（M）来排序
# du -s /usr/* | sort -rn | head #选出排在前面的10个
# du -s /usr/* | sort -rn | tail #选出排在后面的10个
```
##  磁盘和分区
```
# mount | column -t      # 查看挂接的分区状态
# fdisk -l               # 查看所有分区
# swapon -s              # 查看所有交换分区
# hdparm -i /dev/hda     # 查看磁盘参数(仅适用于IDE设备)
# dmesg | grep IDE       # 查看启动时IDE设备检测状况
```
## 网络
``` 
# ifconfig               # 查看所有网络接口的属性
# iptables -L            # 查看防火墙设置
# route -n               # 查看路由表
# netstat -lntp          # 查看所有监听端口
# netstat -antp          # 查看所有已经建立的连接
# netstat -s             # 查看网络统计信息
```
## 进程
```
# ps -ef                 # 查看所有进程
# top                    # 实时显示进程状态
```
## 用户
``` 
# w                      # 查看活动用户
# id <用户名>            # 查看指定用户信息
# last                   # 查看用户登录日志
# cut -d: -f1 /etc/passwd   # 查看系统所有用户
# cut -d: -f1 /etc/group    # 查看系统所有组
# crontab -l             # 查看当前用户的计划任务
```
## 服务
```
# chkconfig --list       # 列出所有系统服务
# chkconfig --list | grep on    # 列出所有启动的系统服务
```
## 程序
``` 
# rpm -qa                # 查看所有安装的软件包
```

## 执行 php代码
```
# php -r 'print_r($_SERVER);'
# php -r 'echo date('Ymdhis');'
```

## 压缩/解压
```
tar命令
# tar zxvf FileName.tar # 解包
# tar czvf FileName.tar DirName #　打包
gz命令
　# 解压1： gunzip FileName.gz 
　# 解压2：gzip -d FileName.gz
　# 压缩：gzip FileName
 
　	   #　====.tar.gz 和 .tgz ====
　#　解压：tar zxvf FileName.tar.gz 　
　#　压缩：tar zcvf FileName.tar.gz DirName 　
    # 压缩多个文件：tar zcvf FileName.tar.gz DirName1 DirName2 DirName3 ... 　
bz2命令
　#　解压1：bzip2 -d FileName.bz2
　#　解压2：bunzip2 FileName.bz2
　#　压缩： bzip2 -z FileName
　#　======.tar.bz2 =====
　#　解压：tar jxvf FileName.tar.bz2
　#　压缩：tar jcvf FileName.tar.bz2 DirName

bz命令
#　　解压1：bzip2 -d FileName.bz
#　　解压2：bunzip2 FileName.bz
#　　压缩：未知
#　　====== .tar.bz =====
#　　解压：tar jxvf FileName.tar.bz
Z命令
#　　解压：uncompress FileName.Z
#　　压缩：compress FileName
​
#　=====　.tar.Z ======
#　　解压：tar Zxvf FileName.tar.Z
#　　压缩：tar Zcvf FileName.tar.Z DirName
zip命令
　#　解压：unzip FileName.zip
　#　压缩：zip FileName.zip DirName
```

##  curl命令请求
> *linux curl命令可以使用下面参数设置http(s)代理、socks代理，已经设置它们的用户名、密码以及认证方式*

参数 | 用法
---|---
 -x host:port<br/> -x [protocol://[user:pwd@]host[:port]<br/>--proxy [protocol://[user:pwd@]host[:port]<br/>| 使用HTTP代理访问；如果未指定端口，默认使用8080端口;<br/>protocol默认为http_proxy，其他可能的值包括：<br/>http_proxy、HTTPS_PROXY、socks4、socks4a、socks5；<br/>如：<br/>--proxy 8.8.8.8:8080；<br/>-x "http_proxy://aiezu:123@aiezu.com:80"
--socks4 <host[:port]><br/>--socks4a <host[:port]><br/>--socks5 <host[:port]><br/> | 使用SOCKS4代理；<br/>使用SOCKS4A代理；<br/>使用SOCKS5代理；<br/>此参数会覆盖“-x”参数；<br/>
--proxy-anyauth<br/>--proxy-basic<br/>--proxy-diges<br/>--proxy-negotiate<br/>--proxy-ntlm<br/> | 代理认证方式，参考：<br/> --anyauth <br/> --basic <br/> --diges <br/> --negotiate <br/> --ntlm <br/>
-U <user:password> <br/>--proxy-user <user:password> | 设置代理的用户名和密码；


- Linux curl命令设置代理举例：
>1、linux curl命令设置http代理：
```
# 指定http代理IP和端口
curl -x 113.185.19.192:80 http://aiezu.com/test.php
curl --proxy 113.185.19.192:80 http://aiezu.com/test.php
 
#指定为http代理
curl -x http_proxy://113.185.19.192:80 http://aiezu.com/test.php
 
#指定为https代理
curl -x HTTPS_PROXY://113.185.19.192:80 http://aiezu.com/test.php
 
#指定代理用户名和密码，basic认证方式
curl -x aiezu:123456@113.185.19.192:80 http://aiezu.com/test.php
curl -x 113.185.19.192:80 -U aiezu:123456 http://aiezu.com/test.php
curl -x 113.185.19.192:80 --proxy-user aiezu:123456 http://aiezu.com/test.php
 
#指定代理用户名和密码，ntlm认证方式
curl -x 113.185.19.192:80 -U aiezu:123456 --proxy-ntlm http://aiezu.com/test.php
 
#指定代理协议、用户名和密码，basic认证方式
curl -x http_proxy://aiezu:123456@113.185.19.192:80 http://aiezu.com/test.php
```
>2、Linux curl命令设置socks代理：
```
#使用socks4代理，无需认证方式
curl --socks4 122.192.32.76:7280 http://aiezu.com/test.php
curl -x socks4://122.192.32.76:7280 http://aiezu.com/test.php
 
#使用socks4a代理，无需认证方式
curl --socks4a 122.192.32.76:7280 http://aiezu.com/test.php
curl -x socks4a://122.192.32.76:7280 http://aiezu.com/test.php
 
#使用socks5代理，basic认证方式
curl --socks5 122.192.32.76:7280 -U aiezu:123456 http://aiezu.com/test.php
curl -x socks5://aiezu:123456@122.192.32.76:7280 http://aiezu.com/test.php
 
#使用socks5代理，basic认证方式，ntlm认证方式
curl -x socks5://aiezu:123456@122.192.32.76:7280 --proxy-ntlm http://aiezu.com/test.php

```

## 查看 CPU 核数
```
几个cpu
 
# more /proc/cpuinfo |grep "physical id"|uniq|wc -l
每个cpu是几核（假设cpu配置相同）

 
# cat /proc/cpuinfo | grep processor
1. 查看物理CPU的个数

 
#cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l



2. 查看逻辑CPU的个数

 
#cat /proc/cpuinfo |grep "processor"|wc -l


3. 查看CPU是几核

 
# cat /proc/cpuinfo |grep "cores"|uniq


4. 查看CPU的主频

 
# cat /proc/cpuinfo |grep MHz|uniq
 
  ● 获得CPU的详细信息：
 
# cat /proc/cpuinfo
  ● 用命令判断几个物理CPU，几个核等：逻辑CPU个数：

 
# cat /proc/cpuinfo | grep "processor" | wc -l

  ● 物理CPU个数：

 
# cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l
  ● 每个物理CPU中Core的个数：

 
# cat /proc/cpuinfo | grep "cpu cores" | wc -l
5 . 判断是否为超线程？
     >>> 如果有两个逻辑CPU具有相同的”core id”，那么超线程是打开的。
       
  ● 每个物理CPU中逻辑CPU(可能是core, threads或both)的个数：
 
# cat /proc/cpuinfo | grep "siblings"
```
