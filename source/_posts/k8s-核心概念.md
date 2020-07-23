---
title: Kubernetes核心概念和术语
date: 2020-01-03 00:05:03
tags:
categories:
---


## Kubernetes架构图

![](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/k8s架构图.jpg)

## 核心概念

### Pod
Kubernetes的基本调度单元称为“Pod”，一个Pod包含一个或多个容器，这样可以保证同一个Pod内的容器运行在同一个宿主机上，并且可以共享资源，这些容器使用相同的网络命名空间、IP地址和端口。Kubernetes中的每个Pod都被分配一个唯一的IP地址，这样就可以允许应用程序使用端口而不会有冲突的风险。另外，同一个Pod的容器还可共享一块存储卷空间，可以定义一个卷，如本地磁盘目录或网络磁盘。<font color=red>在Kubernetes中创建、调度和管理的最小单位是Pod，而不是Docker容器。</font>用户可以通过Kubernetes API手动管理Pod，也可以委托给控制器来管理Pod

### Controller

Kubernetes集群的设计中，Pod是有生命周期的对象。用户通过手工创建或由Controller（控制器）直接创建的Pod对象会被“调度器”（Scheduler）调度至集群中的某工作节点运行，待到容器应用进程运行结束之后正常终止，随后就会被删除。

**控制器本身也是一种资源类型，它有着多种实现，其中与工作负载相关的实现如Replication Controller、Deployment、StatefulSet、DaemonSet和Jobs等，也可统称它们为Pod控制器。**

#### 1.Replication Controller

**Replication Controller用于管理、控制Pod的副本数，用于解决Pod的扩容、缩容问题。**通常，分布式应用为了性能或高可用性的考虑，需要复制多份资源，并且根据负载情况实现动态伸缩。通过Replication Controller，我们可以指定一个应用需要几份副本，Kubernetes将为每份副本创建一个Pod，并且保证实际运行Pod数量总是与该副本数量相等。如果少于指定数量的Pod副本，Replication Controller会重新启动新的Pod副本，反之会“杀死”多余的副本以保证数量不变

#### 2.Deployment

Deployment的主要职责同样是为了保证Pod的数量和健康，而且绝大多数的功能与Replication Controller完全一样，因此可以被看作新一代的Replication Controller。但是，它又具备了Replication Controller不具备的新特性：

-  事件和状态查看：可以查看升级的详细进度和状态
-  回滚：当升级Pod镜像或者相关参数的时候发现问题，可以使用回滚操作回滚到上一个稳定的版本或者指定的版本。
-  版本记录：每一次对Deployment的操作都能保存下来，给予后续可能的回滚使用。
-  暂停和启动：对于每一次升级，都能够随时暂停和启动。
-  多种升级方案: 
  -  Recreate——删除所有已存在的Pod，重新创建新的
  -  RollingUpdate——滚动升级，即逐步替换的策略。滚动升级时支持更多的附加参数，例如，设置最大不可用Pod数量、最小升级间隔时间等。

![image-20200706130725244](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/image-20200706130725244.png)

#### 3.StatefulSet

StatefulSet旨在与有状态的应用及分布式系统一起使用。然而，在Kubernetes上管理有状态应用和分布式系统是一个宽泛而复杂的任务。

StatefulSet使用起来相对复杂，当应用具有以下特点时才建议使用StatefulSet：

 - 有唯一的稳定的网络标识符需求。
 - 有稳定性、持久化数据存储需求。
 - 有序的部署和扩展需求。
 - 有序的删除和终止需求。
 - 有序的自动滚动更新需求。

### Service
 Service是真实应用服务的抽象，定义了Pod的逻辑集合和访问这个集合的策略。Service是一组协同工作的Pod，就像多层架构应用中的一层，是Pod的路由代理抽象，用于解决Pod之间的服务发现问题。<font color=blue>因为Pod的运行状态可动态变化（比如，Pod迁移到其他机器或者在缩容过程中被终止等），所以访问端不能以固定IP的方式去访问该Pod提供的服务。Service的引入旨在保证Pod的动态变化对访问端透明，访问端只需要知道Service的地址，由Service来提供代理。</font>构成服务的Pod组通过Label选择器来定义。Kubernetes通过给服务分配静态IP地址和域名来提供服务发现机制，并且以轮询调度的方式将流量负载均衡到能与选择器匹配的Pod的IP地址的网络连接上（即使是故障导致Pod从一台机器移动到另一台机器）。默认情况下，一个服务会暴露在集群中（例如，多个后端Pod可能被分组成一个服务，前端Pod的请求在它们之间负载均衡）；但是，一个服务也可以暴露在集群外部（例如，从客户端访问前端Pod）。


### Label及Label Selector (标签及标签选择器)

Kubernetes将称为`Label`的键-值对附加到系统中的任何API对象上，如Pod、Service、Replication Controller等。实际上，Kubernetes中的任意API对象都可以通过Label进行标识。<font color=red>每个API对象可以有多个Label，但每个Label的Key只能是唯一值。</font>相应地，Lable Selector则是针对匹配对象的标签来进行的查询。Label和LabelSelector是Kubernetes中的主要分组机制，用于确定操作适用的组件。



## Kubernetes集群组件

### Master组件

Master是Kubernetes分布式集群的管理控制中枢，所有任务分配都来自于此。<font color=red> **Master节点主要由四个模块组成：API Server、scheduler、controller manager、etcd。** </font>

#### 1. API Server

提供了Kubernetes资源对象的唯一操作入口，其他组件都必须通过它提供的API来操作资源数据，通过对相关的资源数据“全量查询+变化监听”，这些组件可以很“实时”地完成相关的业务功能，比如新建Pod的请求一旦被提交到API服务器中，Controller Manager就会立即发现并开始调度。

#### 2. scheduler

负责集群的资源调度，以及Pod在集群节点中的调度分配。资源调度在整个Kubernetes管理节点中是一个独立的组件，也就意味着可以根据客户需求定制化开发自己的调度器或者替换成第三方调度器。

#### 3.controller manager

集群内部的管理控制中心，其主要目的是实现Kubernetes集群的故障检测和恢复的自动化工作。比如，根据Replication Controller（RC，复制控制器）的定义完成Pod的复制或移除，以确保Pod实例数符合RC副本的定义；根据Service与Pod的管理关系，完成服务的Endpoint对象的创建和更新：其他诸如Node的发现、管理和状态监控、已“杀死”容器所占磁盘空间及本地缓存的镜像文件的清理等工作也都由Controller Manager来完成

#### 4.etcd

Etcd是Kubernetes集群中的一个十分重要的组件，用于保存集群所有的网络配置和对象的状态信息。整个kubernetes系统中一共有两个服务需要用到etcd用来协同和存储配置，分别是:

- 网络插件: 用etcd存储网络的配置信息。
- kubernetes本身，包括各种对象的状态和元信息配置。

### Node组件
Node也称为Worker或Minion节点，是主从分布式集群架构的计算单元，是分配给Pod并运行Pod的宿主机。Kubernetes集群中的每个计算节点都必须运行Docker引擎以及下面提到的组件，以便与这些容器的网络配置进行通信。<font color=red>每个Node节点主要由三个模块组成：kubelet、kube-proxy、runtime(容器运行时环境)</font>



#### 1. kubelet

kubelet是运行于工作节点之上的守护进程，它从APIServer接收关于Pod对象的配置信息并确保它们处于期望的状态。kubelet会在API Server上注册当前工作节点，定期向Master汇报节点资源使用情况，并通过cAdvisor监控容器和节点的资源占用状况。
#### 2. runtime(容器运行时环境) :
每个Node都要提供一个容器运行时（ContainerRuntime）环境，它负责下载镜像并运行容器。kubelet并未固定链接至某容器运行时环境，而是以插件的方式载入配置的容器环境。这种方式清晰地定义了各组件的边界。
#### 3. kube-proxy :
每个工作节点都需要运行一个kube-proxy守护进程，它能够按需为Service资源对象生成iptables或ipvs规则，从而捕获访问当前Service的ClusterIP的流量并将其转发至正确的后端Pod对象。



## Kubernetes 运行概要图

![](https://gitee.com/QingHui/picGo-img-bed/raw/master/img/3511337-15e18b42c884866a.png)