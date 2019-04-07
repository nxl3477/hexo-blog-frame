---
title: 【容器】docker命令手册
date: 2019-03-17 10:33:00
categories: 容器
tags: [Docker, 容器]
---



**使用`docker` 查看所有命令**


## 基本命令


| 命令 | 功能 | 示例 |
| :------:| :------: | :------: |
| FROM | 声明底包来源 | FROM centos | 
| RUN | 构建命令--只在构建镜像中使用  | RUN yum install wget -y |
| WORKDIR | docker中必须用此切换目录  | WORKDIR Python-3.7.0 |
| CMD | 运行命令  | CMD python3 -m http.server |
| docker build 'dockerfile的路径' | 构建新镜像  |  |

## 进程相关命令
> 在docker client中使用

| 命令 | 功能 | 示例 |
| :------:| :------: | :------: |
| docker ps | 查看进城 | docker ps | 
| docker kill 'id' | 杀死指定容器 | docker kill 123 | 
| docker rename | 容器改名 |  | 
| docker restart  | 重启容器 |  | 
| docker rm  | 移除一个或多个'容器'(停止状态的容器) |  |
| docker rmi  | 移除一个或多个'镜像' |  |







## python项目配置文件分析

```DockerFile
# 前两行固定
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













