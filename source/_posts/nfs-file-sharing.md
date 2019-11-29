---
title: 记在Linux中搭建nfs文件共享的一次经历
date: 2016-04-19 18:41:53
tags:
 - nfs
categories:
 - 服务搭建
---

# 1.服务器节点介绍

## 1.1 服务节点
``` 
主机名: server.hui.local 
IP地址: 192.168.1.101/24
```

## 1.2 客户端节点
```
主机名: client.hui.local 
IP地址: 192.168.1.102/24
```


# 2. 服务节点部署

## 2.1 安装NFS相关软件包

```$xslt
# yum install nfs-utils nfs-utils-lib
# systemctl enable rpcbind 
# systemctl enable nfs-server 
# systemctl enable nfs-lock 
# ystemctl enable nfs-idmap
# systemctl start rpcbind 
# systemctl start nfs-server 
# systemctl start nfs-lock 
# systemctl start nfs-idmap
```

## 2.2. 创建共享目录

在服务器中创建名为/var/share的共享目录，让客户端用户读取和写入该目录中的文件

```$xslt
# mkdir /var/share 
# chmod 777 /var/share
```


## 2.3 导出共享目录

在NFS服务器上导出共享目录：编辑文件/etc/exports，修改如下:

```$xslt
# /var/share/  192.168.1.0/24(rw,sync,no_root_squash,no_all_squash）
```

**代码说明:**

```$xslt
/var/share - 共享目录
192.168.1.0/24 - 客户端的IP地址范围
rw  - 共享文件夹同步的可写权限
sync - 同步共享目录
no_root_squash - 启用root权限
no_all_squash - 启用用户权限
```


## 2.4 重启NFS服务

```$xslt
# systemctl restart nfs-server
```


# 3.客户端节点部署

## 3.1 安装NFS相关软件包

```$xslt
# yum install nfs-utils nfs-utils-lib
```


## 3.2 启用并启动NFS服务
```$xslt
# systemctl enable rpcbind 
# systemctl enable nfs-server 
# systemctl enable nfs-lock 
# systemctl enable nfs-idmap

# systemctl start rpcbind 
# systemctl start nfs-server 
# systemctl start nfs-lock 
# systemctl start nfs-idmap
```



## 3.3 挂载NFS共享

创建一个挂载点以挂载我们之前在服务节点中创建的共享文件夹 '/var/share'
```$xslt
# mkdir /var/nfs_share
# mount -t nfs 192.168.1.101：/var/share  /var/nfs_share
```


## 3.4 卸载NFS共享

**卸载:**
```
# umount /var/nfs_share
```
**强制卸载:**
```
# umount -lf /var/nfs_share
```


## 4. 注意事项
- 服务端节点应做文件备份，如果客户端把文件误删除，则服务端对应的文件也会丢失。

