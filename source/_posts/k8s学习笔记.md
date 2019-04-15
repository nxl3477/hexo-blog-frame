---
title: k8s学习笔记
date: 2019-04-09 12:13:11
categories: 容器技术
tags: [Kubernetes, 容器技术]
---

## 什么是K8s
* Kubernetes，因为首尾字母中间有8个字符，所以被简写成 K8s。
* K8s 是底层资源与容器间的一个抽象层，如果和单机架构类比，可以算作是一个分布式时代的 Linux。
* K8s 是 Google 开源的容器集群管理系统。在 Docker 技术的基础上，为容器化的应用提供`部署运行`、`资源调度`、`服务发现`和`动态伸缩`等一系列完整功能，提高了大规模容器集群管理的便捷性。


## K8S的特点
* k8s是一个管理容器的工具，也是管理应用整个生命周期的一个工具，从创建应用，应用的部署，应用提供服务，扩容缩容应用，应用更新，而且可以做到`故障自愈`。
* 可移植：支持公有云，私有云，混合云；
* 可扩展：模块化，热插拨，可组合；
* 自愈：自动替换，自动重启，自动复制，自动扩展。

> 故障自愈场景比如机房工作人员误操作将某一排服务器断电了， k8s就会认为是故障， 自动扩充备用服务器

## K8S的管理步骤
在k8s进行管理应用的时候，基本步骤是：
* 创建集群
* 部署应用
* 发布应用
* 扩展应用
* 更新应用

## K8S的架构结构

* 生态系统
  * 外部生态（k8s运行过程中产出的来判断，如：日志、监控、配置功能、自动化发布、工作流）
  * 内部生态（系统本身所依赖的东西， 如：镜像管理、镜像仓库、集群配置管理）
* 接口层
  * 用于开发和管理生态系统层
* 管理层
  * 系统层面的调度，比如：自动扩展， 监视、资源状态
* 应用层
  * 部署及路由
    * 处理有状态应用和无状态应用
    * 路由根据内部的`dns`服务器
* 核心层
  * k8s的核心功能， 提供程序内部接口
* 核心模块及插件

![k8s架构结构](http://img.nixiaolei.com/2019-04-13-13-48-43.png)


k8s是和`TCP/IP`协议很像都是不允许跨层的， 当生态系统层的功能不够使用时， 你只能通过接口层来开发，不能使用更下层的东西了



## k8s相关概念
* **主机（Master）**：用于控制 `Kubernetes` 节点的计算机。所有任务分配都来自于此。
* **节点（Node）**：执行请求和分配任务的计算机。由 `Kubernetes` 主机负责对节点进行控制。
* **容器集（Pod）**：部署在单个节点上的，且包含一个或多个容器的容器组。同一容器集中的所有容器共享同一个 IP 地址、IPC、主机名称及其它资源。容器集会将`网络`和`存储`从底层容器中抽象出来。这样，您就能更加轻松地在集群中移动容器。
* **复制控制器（Replication controller）**： 用于控制应在集群某处运行的完全相同的容器集副本数量。
* **服务（Service）**：服务可将工作定义与容器集分离。`Kubernetes` 服务代理会自动将服务请求分配到正确的容器集——无论这个容器集会移到集群中的哪个位置，即使它已被替换。
* **Kubelet**： 这是一个在节点上运行的服务，可读取容器清单，确保指定的容器启动并运行。
* **kubectl**： `Kubernetes` 的命令行配置工具

> 如果数据库安装于容器内部， 则在数据膨胀后，想要升级数据库就变得十分的困难， 容器集将他从容器中抽象出来便于移动管理容器

## k8s整体流程

* 容器间都由`Master`统一管理， 一个容器想知道另一个容器的服务， 就必须询问`master`, 由`master`引导调度 
* `Linux` 的`etc`相当于`window`的注册表, `Linux`中的配置文件和`window`不一样， `window`是集中管理 ，`Linux`是分散管理,  `etcd`就是为了解决多集群时配置文件难以维护的问题

![k8s架构结构](http://img.nixiaolei.com/2019-04-13-14-57-25.png)







## 安装K8S

在`Linux`下安装单机版的集群环境
以root身份执行以下操作：

### 1. **关闭`Linux`防火墙**
> 如果不关闭可能会在`SELinux` 安全模块上出问题

```shell
systemctl stop firewalld

systemctl disable firewalld
```
### 2. **安装`Kubernetes`和依赖组件`etcd`**

```shell
yum install -y etcd kubernetes
```

### 3. **修改配置**
> docker 中的 `SElinux`安全模块也要关闭

* Docker配置文件/etc/sysconfig/docker, OPTIONS='--selinux-enabled=false --insecure-registry gcr.io'
* Kubernetes apiservce配置文件/etc/kubernetes/apiserver,把--admission-control参数中的ServiceAccount删除


### 4. **按顺序启动所有的服务**
* 提供配置存储: `systemctl start etcd`
* k8s node的重要服务: `systemctl start docker`
* 提供客户端接口: `systemctl start kube-apiserver`
* systemctl start kube-controller-manager
* 提供管理功能： `systemctl start kube-scheduler`
* 提供pod功能: `systemctl start kubelet`
* 提供路由请求: `systemctl start kube-proxy`


## K8S相关资源
1、官网
> https://kubernetes.io

2、Chart 应用仓库
> https://hub.kubeapps.com/

3、中文手册
> https://www.kubernetes.org.cn/docs