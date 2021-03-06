---
title: 【容器】Docker入门教程
date: 2019-03-16 18:00:14
categories: 容器技术
tags: [Docker, 容器技术]
---

## 1.1 Docker简介


* Docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器。开发者在笔记本上编译测试通过的容器可以批量地在生产环境中部署，包括VMs（虚拟机）、bare metal、OpenStack 集群和其他的基础应用平台。
* Docker通常用于如下场景：
  * web应用的自动化打包和发布；
  * 自动化测试和持续集成、发布；
  * 在服务型环境中部署和调整数据库或其他的后台应用；
  * 从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。



## 2.1 Docker vs. VM
* VM：
  * 运行在宿主机之上的完整的操作系统
  * 运行自身操作系统会占用较多的资源
* Docker：
  * Docker更加轻量高效
  * 对系统资源的利用率很高
  * 比虚拟机技术更为轻便、快捷
  * 隔离效果不如VM


![Docker与VM](http://img.nixiaolei.com/2019-04-06-22-42-11.png)

*** 
总的来说， 虚拟机是在操作系统上又装了一个操作系统，运行操作系统特别消耗资源

而`Docker`是一种类似于隔离的技术， 由`CPU` 和`Linux`内核提供的虚拟化技术， 公用你宿主机上的内核， 在`Docker`中再装了一层`shell`, 并且`Docker`和其他`Docker`之间是可以通过内核相互进行通信的(`Linux`进程之间必须经过内核)


## 3.1 镜像与容器

image是静态的概念， 比如： 你的`exe`文件

container是动态的概念， 比如： 你执行`exe`后的进程

## 4.1 Docker相关概念
* Docker是CS架构，主要有两个概念：
* Docker daemon:
  * 运行在宿主机上
  * Docker守护进程
  * 用户通过Docker client(Docker命令)与Docker daemon交互
* Docker client:
  * Docker 命令行工具，是用户使用Docker的主要方式
  * Docker client与Docker daemon通信并将结果返回给用户
  * Docker client也可以通过socket或者RESTful api访问远程的
  * Docker daemon

## 4.2 相关概念详解

`Docker`最底层是宿主的操作系统内核， 

再上层就是底包， 底包就是存放`bash`以及`bash`所依赖的基本环境， 每个系统都有不一样的底包， 你可以自己去做底包， 也可以到[dockerHub](https://www.docker.com/products/docker-hub)里找

![Docker结构](http://img.nixiaolei.com/2019-04-06-22-56-37.png)


在此之上一层一层的往上累加，比如`Node`、`Mysql`啥的, 最后扔到容器里跑, 就像下面这张图， 一层一层的往上叠加，跟码头的集装箱一样

![Docker上层结构](http://img.nixiaolei.com/2019-04-06-23-14-37.png)


## 5.1 Dockerfile 脚本
> 规定了如何创建Image

* Dockerfile 概念
* Dockerfile 文件格式
* 构建镜像
* 镜像标签
* 修改容器内容


![配置文件](http://img.nixiaolei.com/2019-04-07-10-26-12.png)


## 6.1 配置文件分析

```DockerFile
# 前两行固定写法~
# 你的底包来源
FROM centos
# 作者(用于上传时使用)
MAINTAINER nxl <nxl3477@foxmail.com>

# RUN构建命令--只在构建镜像中使用    ( -y 表示全部yes)
RUN yum install gcc automake autoconf libtool make -y  
# python 依赖于 zlib
RUN yum install zlib zlib-devel libffi-devel -y
# 安装 wget
RUN yum install wget -y
# 下载python安装包
RUN wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
# 解压缩
RUN tar -zxvf Python-3.7.0.tgz
# 切换目录
WORKDIR Python-3.7.0

# 编译安装
RUN ./configure
RUN ls -al
RUN make && make install 

# CMD 运行命令， 唤起python自带的服务器
CMD python3 -m http.server
```