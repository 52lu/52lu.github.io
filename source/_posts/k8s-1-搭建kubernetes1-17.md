---
title: K8s-安装Kubernetes1.17
date: 2020-01-01 00:31:37
tags:
 - Kubernetes
categories:
 - 微服务
---


# 1.环境总览
## 1.1 镜像环境
- 操作系统: Ubuntu 18.04
- Docker版本: Docker 19.03.5.ce
- Kubernetes: v1.17.0


服务器 | IP | 角色
---|---|---
serve01 | 192.168.0.116 | master
node01  |192.168.122.11 | node
node02  |192.168.122.12 | node
node03  |192.168.122.13 | node



# 2.配置修改
## 2.1 修改时区

```
# 查看当前时区
[root@node /]# timedatectl
      Local time: Thu 2019-12-12 06:24:49 UTC
  Universal time: Thu 2019-12-12 06:24:49 UTC
        RTC time: Thu 2019-12-12 06:24:49
       Time zone: UTC (UTC, +0000)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
      
# 修改时区
[root@node /]# timedatectl set-timezone Asia/Shanghai

# 查看修改后时区
[root@node /]# timedatectl
      Local time: Thu 2019-12-12 14:25:16 CST
  Universal time: Thu 2019-12-12 06:25:16 UTC
        RTC time: Thu 2019-12-12 06:25:16
       Time zone: Asia/Shanghai (CST, +0800)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
[root@node /]#
```


## 2.2 修改主机名
在同一局域网中主机名不应该相同，所以我们需要做修改，下列操作步骤为修改 18.04 版本的 Hostname

- 查看当前 Hostname

```
root@node:/etc/apt# hostnamectl
   Static hostname: node
         Icon name: computer-vm
           Chassis: vm
        Machine ID: b4da8301e28e41a783ba960205e1d4d0
           Boot ID: 4675071ab63c4b8aa26758456afe7a57
    Virtualization: kvm
  Operating System: Ubuntu 18.04.3 LTS
            Kernel: Linux 4.15.0-72-generic
      Architecture: x86-64
```
- 修改 Hostname

```
# 使用 hostnamectl 命令修改，其中 kubernetes-master 为新的主机名
hostnamectl set-hostname kubernetes-master
```

- 修改 cloud.cfg
如果 cloud-init package 安装了，需要修改 cloud.cfg 文件。该软件包通常缺省安装用于处理 cloud
```sh
# 如果有该文件
vi /etc/cloud/cloud.cfg

# 该配置默认为 false，修改为 true 即可
preserve_hostname: true
```

## 2.3 关闭防火墙服务
各Node运行的kube-proxy组件均要借助iptables或ipvs构建Service资源对象，该资源对象是Kubernetes的核心资源之一。出于简化问题复杂度之需，这里需要事先关闭所有主机之上的iptables或firewalld服务：


```sh
# 关闭防火墙
root@node:/etc/apt# ufw disable
Firewall stopped and disabled on system startup
# 查看防火墙状态
root@node:/etc/apt# ufw status
Status: inactive
```


## 2.4 关闭并禁用SELinux

- 临时关闭

```
setenforce 0
```
- 永久关闭
编辑/etc/sysconfig/selinux文件，以彻底禁用SELinux。
```
[root@node /]#  sed -i 's@^\(SELINUX=\).*@\1disabled@' /etc/sysconfig/selinux
```

## 2.5 禁用Swap设备
kubeadm默认会预先检查当前主机是否禁用了Swap设备，并在未禁用时强制终止部署过程。因此，在主机内存资源充裕的条件下，需要禁用所有的Swap设备。
关闭Swap设备，需要分两步完成。
- 第一步:关闭交换空间

```
# 关闭前查看
root@node:~# free -m
              total        used        free      shared  buff/cache   available
Mem:           1993         116        1126           0         750        1724
Swap:          2047           0        2047

# 关闭
root@node:~# swapoff -a

# 关闭后查看
root@node:~# free -m
              total        used        free      shared  buff/cache   available
Mem:           1993         115        1127           0         750        1725
Swap:             0           0           0
```
- 第二步:避免开机启动交换空间
注释 /etc/fstab 中的 swap

```
root@node:~# cat /etc/fstab
UUID=76051108-098a-49c8-be7f-6be376b069e4 / ext4 defaults 0 0
# /swap.img	none	swap	sw	0	0
```

## 2.6 修改apt源
编辑 /etc/apt/sources.list 删除原先所有内容，添加以下阿里源
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```
## 2.7 设置固定IP
- 编辑 vim /etc/netplan/50-cloud-init.yaml 配置文件
```
network:
    ethernets:
        ens3:
            #IP地址
            addresses: [192.168.122.11/24]
            # 网关
            gateway4: 192.168.122.1
            # dns地址
            nameservers:
                    addresses: [192.168.122.1]
    version: 2
```
- 使配置生效
```
[root@node /]# netplan apply
```
## 2.8 DNS
如果发现，服务器不能访问外网，则需要修改DNS

### 方法一
- 停止 systemd-resolved 服务

<font color=red> 如果不停止systemd-resolved 服务，则会重写/etc/resolv.conf，把之前的配置覆盖掉
```
[root@node /]# systemctl stop systemd-resolved
```
- 修改配置文件

`vim /etc/resolv.conf`，将 nameserver修改114.114.114.114,如下

```
root@node01:~# cat /etc/resolv.conf
# This file is managed by man:systemd-resolved(8). Do not edit.
#
# This is a dynamic resolv.conf file for connecting local clients to the
# internal DNS stub resolver of systemd-resolved. This file lists all
# configured search domains.
#
# Run "systemd-resolve --status" to see details about the uplink DNS servers
# currently in use.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

# nameserver 127.0.0.53
# 把原来的修改为
nameserver 114.114.114.114
options edns0
```

### 方法二
- 修改配置文件
`vim  /etc/systemd/resolved.conf`

```sh
[Resolve]
# 此处修改
DNS=114.114.114.114
#FallbackDNS=
#Domains=
#LLMNR=no
#MulticastDNS=no
#DNSSEC=no
#Cache=yes
#DNSStubListener=yes
~
```
- 然后重启

# 3 安装docker
## 3.1 安装前操作

```
# 安装所需依赖
root@node:~# apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# 安装 GPG 证书
root@node:~# curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 新增软件源信息
root@node:~# add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# 更新软件源
root@node:~# apt-get -y update
# 安装 Docker CE 版
root@node:~# apt-get -y install docker-ce
```

## 3.2 设置开机启动
```sh
# 开机启动
root@node:~# systemctl enable docker
Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker
```

## 3.3 配置加速器

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）
```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```
重启docker
```
root@node:~# systemctl daemon-reload
root@node:~# systemctl restart docker
```


# 4. 安装kubeadm
kubeadm 是 kubernetes 的集群安装工具，能够快速安装 kubernetes 集群。

## 4.1 配置kubernetes软件源
```sh
# 安装 GPG 证书
root@node:~# curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -

# 写入软件源；注意：我们用系统代号为 bionic，但目前阿里云不支持，所以沿用 16.04 的 xenial
root@node:~# cat > /etc/apt/sources.list.d/kubernetes.list  << EOF 
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

# 更新软件源
root@node:~# apt-get update  -y
```

## 4.2 安装 kubeadm，kubelet，kubectl
```
root@node:~# apt-get install -y kubelet kubeadm kubectl

Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  conntrack cri-tools kubernetes-cni socat
The following NEW packages will be installed:
  conntrack cri-tools kubeadm kubectl kubelet kubernetes-cni socat
0 upgraded, 7 newly installed, 0 to remove and 76 not upgraded.
Need to get 51.6 MB of archives.
After this operation, 272 MB of additional disk space will be used.
Get:1 http://mirrors.aliyun.com/ubuntu bionic/main amd64 conntrack amd64 1:1.4.4+snapshot20161117-6ubuntu2 [30.6 kB]
Get:2 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 cri-tools amd64 1.13.0-00 [8776 kB]
Get:3 http://mirrors.aliyun.com/ubuntu bionic/main amd64 socat amd64 1.7.3.2-2ubuntu2 [342 kB]
Get:4 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubernetes-cni amd64 0.7.5-00 [6473 kB]
Get:5 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubelet amd64 1.17.0-00 [19.2 MB]
Get:6 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubectl amd64 1.17.0-00 [8742 kB]
Get:7 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial/main amd64 kubeadm amd64 1.17.0-00 [8059 kB]
Fetched 51.6 MB in 13s (4034 kB/s)
Selecting previously unselected package conntrack.
(Reading database ... 67247 files and directories currently installed.)
Preparing to unpack .../0-conntrack_1%3a1.4.4+snapshot20161117-6ubuntu2_amd64.deb ...
Unpacking conntrack (1:1.4.4+snapshot20161117-6ubuntu2) ...
Selecting previously unselected package cri-tools.
Preparing to unpack .../1-cri-tools_1.13.0-00_amd64.deb ...
Unpacking cri-tools (1.13.0-00) ...
Selecting previously unselected package kubernetes-cni.
Preparing to unpack .../2-kubernetes-cni_0.7.5-00_amd64.deb ...
Unpacking kubernetes-cni (0.7.5-00) ...
Selecting previously unselected package socat.
Preparing to unpack .../3-socat_1.7.3.2-2ubuntu2_amd64.deb ...
Unpacking socat (1.7.3.2-2ubuntu2) ...
Selecting previously unselected package kubelet.
Preparing to unpack .../4-kubelet_1.17.0-00_amd64.deb ...
Unpacking kubelet (1.17.0-00) ...
Selecting previously unselected package kubectl.
Preparing to unpack .../5-kubectl_1.17.0-00_amd64.deb ...
Unpacking kubectl (1.17.0-00) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../6-kubeadm_1.17.0-00_amd64.deb ...
Unpacking kubeadm (1.17.0-00) ...
Setting up conntrack (1:1.4.4+snapshot20161117-6ubuntu2) ...
Setting up kubernetes-cni (0.7.5-00) ...
Setting up cri-tools (1.13.0-00) ...
Setting up socat (1.7.3.2-2ubuntu2) ...
Setting up kubelet (1.17.0-00) ...
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
Setting up kubectl (1.17.0-00) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Setting up kubeadm (1.17.0-00) ...
```
## 4.3 kubeadm，kubelet，kubectl 功能介绍

- kubeadm：用于初始化 Kubernetes 集群
- kubectl：Kubernetes 的命令行工具，主要作用是部署和管理应用，查看各种资源，创建，删除和更新组件
- kubelet：主要负责启动 Pod 和容器


# 5. 配置kubeadm
安装 kubernetes 主要是安装它的各个镜像，而 kubeadm 已经为我们集成好了运行 kubernetes 所需的基本镜像。但由于国内的网络原因，在搭建环境时，无法拉取到这些镜像。此时我们只需要修改为阿里云提供的镜像服务即可解决该问题。

## 5.1 创建配置
创建配置文件所在的目录: `mkdir -p /usr/local/docker/kubernetes`，也可以在其他地方创建，建议在此处创建。

```
root@node:~# cd /usr/local/docker/kubernetes/
# 获取配置
root@node:/usr/local/docker/kubernetes# kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
W0104 16:59:10.279204   24908 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0104 16:59:10.279269   24908 validation.go:28] Cannot validate kubelet config - no validator is available
```
## 5.2 修改配置
```
# 修改配置为如下内容
apiVersion: kubeadm.k8s.io/v1beta1
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  # 修改为主节点 IP
  advertiseAddress: 192.168.0.116
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: ""
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
# 国内不能访问 Google，修改为阿里云
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
# 确认版本号是否一致
kubernetesVersion: v1.17.0
networking:
  dnsDomain: cluster.local
  # flannel 网络
  podSubnet: "10.244.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
```
## 5.3 查看镜像
```
# 查看所需镜像列表
[root@serve01 kubernetes ]$ kubeadm config images list --config kubeadm.yml
W0105 17:50:34.538613   23098 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0105 17:50:34.538662   23098 validation.go:28] Cannot validate kubelet config - no validator is available
registry.aliyuncs.com/google_containers/kube-apiserver:v1.17.0
registry.aliyuncs.com/google_containers/kube-controller-manager:v1.17.0
registry.aliyuncs.com/google_containers/kube-scheduler:v1.17.0
registry.aliyuncs.com/google_containers/kube-proxy:v1.17.0
registry.aliyuncs.com/google_containers/pause:3.1
registry.aliyuncs.com/google_containers/etcd:3.4.3-0
registry.aliyuncs.com/google_containers/coredns:1.6.5
```
## 5.4 拉取镜像
```
# 拉取镜像
[root@serve01 kubernetes ]$ kubeadm config images pull --config kubeadm.yml
W0105 17:50:51.746492   23130 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0105 17:50:51.746533   23130 validation.go:28] Cannot validate kubelet config - no validator is available
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-apiserver:v1.17.0
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-controller-manager:v1.17.0
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-scheduler:v1.17.0
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-proxy:v1.17.0
[config/images] Pulled registry.aliyuncs.com/google_containers/pause:3.1
[config/images] Pulled registry.aliyuncs.com/google_containers/etcd:3.4.3-0
[config/images] Pulled registry.aliyuncs.com/google_containers/coredns:1.6.5
```

# 6. 使用kubeadm 搭建 kubernetes 集群

## 6.1 启动master节点
执行以下命令初始化主节点，该命令指定了初始化时需要使用的配置文件，追加的 tee kubeadm-init.log 用以输出日志。

```sh
# 通过配置文件启动master节点
[root@serve01 kubernetes ]$ kubeadm init --config=kubeadm.yml | tee kubeadm-init.log
W0105 17:51:53.776645   23228 validation.go:28] Cannot validate kube-proxy config - no validator is available
W0105 17:51:53.776691   23228 validation.go:28] Cannot validate kubelet config - no validator is available
[init] Using Kubernetes version: v1.17.0
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [serve01 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.0.116]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [serve01 localhost] and IPs [192.168.0.116 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [serve01 localhost] and IPs [192.168.0.116 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
W0105 17:52:04.990074   23228 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[control-plane] Creating static Pod manifest for "kube-scheduler"
W0105 17:52:04.991170   23228 manifests.go:214] the default kube-apiserver authorization-mode is "Node,RBAC"; using "Node,RBAC"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[kubelet-check] Initial timeout of 40s passed.
[apiclient] All control plane components are healthy after 43.539082 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.17" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node serve01 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node serve01 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: abcdef.0123456789abcdef
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.0.116:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:dc009a8555c2fabf009d18c190b213d69068e81f438a4c77dbd6f59c5bf3e693
```

### 6.1.1 kubeadm init 的执行过程

- init：指定版本进行初始化操作
- preflight：初始化前的检查和下载所需要的 Docker 镜像文件
- kubelet-start：生成 kubelet 的配置文件 var/lib/kubelet/config.yaml，没有这个文件 kubelet 无法启动，所以初始化之前的 kubelet 实际上启动不会成功
- certificates：生成 Kubernetes 使用的证书，存放在 /etc/kubernetes/pki 目录中
- kubeconfig：生成 KubeConfig 文件，存放在 /etc/kubernetes 目录中，组件之间通信需要使用对应文件
- control-plane：使用 /etc/kubernetes/manifest 目录下的 YAML 文件，安装 Master 组件
- etcd：使用 /etc/kubernetes/manifest/etcd.yaml 安装 Etcd 服务
- wait-control-plane：等待 control-plan 部署的 Master 组件启动
- apiclient：检查 Master 组件服务状态。
- uploadconfig：更新配置
- kubelet：使用 configMap 配置 kubelet
- patchnode：更新 CNI 信息到 Node 上，通过注释的方式记录
- mark-control-plane：为当前节点打标签，打了角色 Master，和不可调度标签，这样默认就不会使用 Master 节点来运行 Pod
- bootstrap-token：生成 token 记录下来，后边使用 kubeadm join 往集群中添加节点时会用到
- addons：安装附加组件 CoreDNS 和 kube-proxy


### 6.1.2 创建所需文件
成功后会有以下提示，挨个执行。
```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 6.1.3 查看节点状态
```
[root@serve01 kubernetes ]$ kubectl get node
NAME      STATUS     ROLES    AGE    VERSION
serve01   NotReady   master   3m4s   v1.17.0
```
## 6.2 节点加入集群
```
root@node01:~# kubeadm join 192.168.0.116:6443 --token abcdef.0123456789abcdef \
>     --discovery-token-ca-cert-hash sha256:dc009a8555c2fabf009d18c190b213d69068e81f438a4c77dbd6f59c5bf3e693
W0105 18:03:00.479801    6753 join.go:346] [preflight] WARNING: JoinControlPane.controlPlane settings will be ignored when control-plane flag is not set.
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.17" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

## 6.3 查看节点状态
我加入了3台node节点，在master 通过 `kubectl get node` 进行查看

```
[root@serve01 kubernetes ]$ kubectl get node
NAME      STATUS     ROLES    AGE     VERSION
node01    NotReady   <none>   3h59m   v1.17.0
node02    NotReady   <none>   8s      v1.17.0
node03    NotReady   <none>   3h54m   v1.17.0
serve01   NotReady   master   4h9m    v1.17.0
```

# 7.安装网络插件
如果没有安装网络，插件节点状态都是`NotReady`
## 7.1 安装flannel网络插件
```
[root@serve01 kubernetes ]$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
> 上述操作会在各个节点下载flannel网络插件相关容器，需要等待..

- 查看节点状态
```
[root@serve01 kubernetes ]$ kubectl get nodes
NAME      STATUS   ROLES    AGE    VERSION
node01    Ready    <none>   2d1h   v1.17.0
node02    Ready    <none>   2d1h   v1.17.0
node03    Ready    <none>   2d1h   v1.17.0
serve01   Ready    master   2d1h   v1.17.0
```

- 查看所有命名空间pod状态
```
[root@serve01 kubernetes ]$ kubectl get pods -A
NAMESPACE     NAME                              READY   STATUS    RESTARTS   AGE
kube-system   coredns-9d85f5447-n8kqg           1/1     Running   0          2d1h
kube-system   coredns-9d85f5447-q4gwf           1/1     Running   0          2d1h
kube-system   etcd-serve01                      1/1     Running   0          2d1h
kube-system   kube-apiserver-serve01            1/1     Running   0          2d1h
kube-system   kube-controller-manager-serve01   1/1     Running   2          2d1h
kube-system   kube-flannel-ds-amd64-fx4zg       1/1     Running   0          24h
kube-system   kube-flannel-ds-amd64-h8kvg       1/1     Running   0          24h
kube-system   kube-flannel-ds-amd64-pbjrl       1/1     Running   0          24h
kube-system   kube-flannel-ds-amd64-tw8mx       1/1     Running   0          24h
kube-system   kube-proxy-2pjlk                  1/1     Running   0          2d1h
kube-system   kube-proxy-gkvt5                  1/1     Running   0          2d1h
kube-system   kube-proxy-l8fh9                  1/1     Running   0          2d1h
kube-system   kube-proxy-vvwzj                  1/1     Running   0          2d1h
kube-system   kube-scheduler-serve01            1/1     Running   2          2d1h
```


**到此集群搭建完毕!**