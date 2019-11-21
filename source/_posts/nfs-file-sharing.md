---
title: 基于Linux搭建nfs文件共享
date: 2016-04-19 18:41:53
tags:
 - nfs
categories:
 - 服务搭建
---
### 1.测试节点
```
NFS服务器主机名：server.unixmen.local 
NFS服务器IP地址：192.168.1.101/24

NFS客户端主机名：client.unixmen.local 
NFS客户端IP地址：192.168.1.102/24
```

### 2.服务器端配置
#### 2.1 安装NFS软件包
```
//使用以下命令在Server系统中安装NFS软件包：

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
#### 2.2 创建共享目录

`现在，让我们在服务器中创建一些共享目录。`

在服务器中创建名为/var/unixmen_share的共享目录，让客户端用户读取和写入该目录中的文件。
```
# mkdir /var/unixmen_share 
# chmod 777 /var/unixmen_share
```
#### 2.3 导出共享目录
`在NFS服务器上导出共享目录：编辑文件/etc/exports，`
添加以下行：
```
# /var/unixmen_share/  192.168.1.0/24(rw,sync,no_root_squash,no_all_squash）

<1> /var/unixmen_share - 共享目录
<2> 192.168.1.0/24 - 客户端的IP地址范围
<3> rw  - 共享文件夹同步的可写权限
<4> sync - 同步共享目录
<5> no_root_squash - 启用root权限
<6> no_all_squash - 启用用户权限
```
#### 2.4 重启NFS服务：
```
# systemctl restart nfs-server
```



---
### 3.客户端
#### 3.1 安装NFS软件包
使用以下命令在客户端系统中安装NFS软件包：

```
# yum install nfs-utils nfs-utils-lib
```
#### 3.2 启用并启动NFS服务
```
# systemctl enable rpcbind 
# systemctl enable nfs-server 
# systemctl enable nfs-lock 
# systemctl enable nfs-idmap

# systemctl start rpcbind 
# systemctl start nfs-server 
# systemctl start nfs-lock 
# systemctl start nfs-idmap
```

###4.在客户端上挂载NFS共享
`创建一个挂载点以挂载我们之前在服务器中创建的共享文件夹'/var/unixmen_share'`。

```
# mkdir /var/nfs_share
```
####4.1 共享服务​​器目录
`将共享从服务​​器挂载到客户端，如下所示`
```
// 挂载
# mount -t nfs 192.168.1.101：/var/unixmen_share  /var/nfs_share 

//强制卸载
umount -lf /var/nfs_share
```
