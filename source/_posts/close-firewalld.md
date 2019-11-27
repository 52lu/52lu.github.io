---
title: 在Centos7中使用防火墙
date: 2015-08-17 12:04:26
tags:
  - linux
categories:
  - 命令使用
---

# 1.查看火墙
## 1.1 使用:firewall-cmd
```
firewall-cmd --state
```
## 1.2 使用:systemctl
```
[root@vpn01 ~]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)
```

# 2.关闭火墙
## 2.1 第一步:关闭firewalld

```
systemctl stop firewalld.service
```
## 2.2 第二步:禁止firewalld开机启动

```
systemctl disable firewalld.service 
```

## 2.3 第三步:禁用selinux

[什么是SELinux?](https://zh.wikipedia.org/wiki/%E5%AE%89%E5%85%A8%E5%A2%9E%E5%BC%BA%E5%BC%8FLinux)
 
1.临时禁用

```
[root@vpn02 ~]# setenforce 0
```
2.永久关闭:修改/etc/selinux/config文件中设置SELINUX=disabled 

```
[root@vpn02 ~]# cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled # 此处设置
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
3.查看selinux状态

```
[root@vpn02 ~]# sestatus
SELinux status:                 disabled
```

