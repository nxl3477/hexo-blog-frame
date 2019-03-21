---
title: 使用Jenkins构建前端持续集成
date: 2019-03-16 12:34:36
categories: CI
tags: [CI, Jenkins, Centos, 前端持续集成]
---
<script type="text/javascript" src="/js/src/bai.js"></script>


1. 安装JDK

直接使用yum 安装的是 `openjdk` , 和sun 公司发布的jdk有略微区别， 但这里我们并不需要使用java,所以使用最快的方式安装


```
yum install -y java
```

2. 安装jenkins

添加Jenkins库到yum库，Jenkins将从这里下载安装。

```
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
```

添加Jenkins库到yum库，Jenkins将从这里下载安装。
```
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
rpm -ivh jenkins-2.7.3-1.1.noarch.rpm
```

3. 配置jenkis的端口

jenkins 的默认端口为 8080 ， 为了避免和其他的应用程序冲突， 所以建议修改一下默认占用的端口

```
vim /etc/sysconfig/jenkins
```

找到修改端口号：

> JENKINS_PORT="8080" 

4. 启动jenkins 


```
service jenkins start/stop/restart
```
* 安装成功后Jenkins将作为一个守护进程随系统启动
* 系统会创建一个“jenkins”用户来允许这个服务，如果改变服务所有者，同时需要修改`/var/log/jenkins`, `/var/lib/jenkins`, 和`/var/cache/jenkins`的所有者
* 启动的时候将从/etc/sysconfig/jenkins获取配置参数
* 默认情况下，Jenkins运行在8080端口，在浏览器中直接访问该端进行服务配置
* Jenkins的RPM仓库配置被加到/etc/yum.repos.d/jenkins.repo


## 配置阶段
安装好`jenkins` 后 ， 我们就可以根据刚才所设置的端口号访问 `jenkins` 服务了 

比如进入 `127.0.0.1:8081` 访问`jenkins`

首先会看到让我们输入初始密码

它提示我们进入 `/var/lib/jenkins/secrets/initialAdminPassword ` 查看

![输入初始密码](http://img.nixiaolei.com/2019-03-09-16-16-27.png)



输入密码无误后， 就是提示我们安装对应的插件了，

左边是推荐安装， 右边是自定义安装，  这里我们建议点击 `推荐安装`

![选择推荐安装](http://img.nixiaolei.com/2019-03-09-16-17-31.png)




安装过程可能稍长

![等待安装完毕 ](http://img.nixiaolei.com/jenkins03.png)


这时我们创建一个 `Jenkins` 账户， 
一定要牢记你的输入账号和密码， 用作之后的登录

![创建账户](http://img.nixiaolei.com/2019-03-09-16-29-08.png)



创建账户之后， 会显示一个 `jenkins` 的资源根路径， 一般不需要修改， 保持默认即可


![资源路径](http://img.nixiaolei.com/2019-03-09-16-32-04.png)




之后就是愉快的`jenkins` 之旅啦

![开始使用Jenkins](http://img.nixiaolei.com/2019-03-09-16-32-37.png)


## 补善插件阶段
我们要做的前端持续集成， 所以 `jenkins` 默认安装的插件 并不能完全的满足我们的需求

进入`jenkins` 主页面后， 找到插件管理

![插件管理](http://img.nixiaolei.com/2019-03-09-16-36-26.png)



首先查看我们必要的插件是否安装好了
* NodeJs Plugin
* Publish Over SSH
* GitHub

如果发现没有安装的就安装， 查漏补缺



## 变量及远程服务器基本配置

插件安装好后, 进入`系统设置` 界面, 进行变量的相关配置, 这里就展示几个可以修改的参数, 大部分都可以不用动, 使用默认就好

![系统设置](http://img.nixiaolei.com/2019-03-09-17-36-54.png)


管理员邮件地址, 推荐填写,  当jenkins 工作完成后会把 构建信息发送至此邮箱

![邮件地址](http://img.nixiaolei.com/2019-03-09-17-42-24.png)

(这条选择填写) 可以选择配置 github 服务器, 并且可以配置多个, 因为你的项目代码有可能在多个服务器上

![github](http://img.nixiaolei.com/2019-03-09-17-43-51.png)


比较重要的就是这里了 ,  配置远程服务器, 也就是你的发布地址

点击新增添加你的服务器

![添加你的服务器](http://img.nixiaolei.com/2019-03-09-17-48-05.png)



点击新增后, 这里有几个关键的参数,  需要注意

![参数注意](http://img.nixiaolei.com/2019-03-09-17-51-39.png)

连接目标服务器肯定需要登录,  下面几个参数就是对应的验证参数, 你可以输入固定的密码, 也可以告诉Jenkins 你的本地私钥路径或私钥的内容,  前提是你配置了 ssh 的免密登录

![配置免密](http://img.nixiaolei.com/2019-03-09-17-54-49.png)


> 填写密钥路径这里你很有可能会出现权限不够的错误， 如下图，


![编路径错误](http://img.nixiaolei.com/2019-03-10-10-43-33.png)




因为`jenkins `的在运行时会自动的创建一个名为`jenkins`的用户， 而这个用户的权限不够，无法访问你的目录

![编运行身份](http://img.nixiaolei.com/2019-03-10-10-46-12.png)



解决方案有两个:
1. 把密钥切换至一个普通用户可以访问的目录， 并修改密钥的所属用户身份和访问权限
2. 把`jenkins` 的运行身份修改为 `root`, 但是会有点安全性问题

本次实践以学习为目的， 所以这里我选择了第二钟

> 相关操作文章: https://blog.csdn.net/jeikerxiao/article/details/80768949


ssh 免密配置成功后， 我们就可以开始创建一个自己的任务了



## 创建任务阶段

回到主页， 找到`新建任务`


![编新建任务](http://img.nixiaolei.com/2019-03-10-10-55-02.png)



进入新建任务后， 有几种不同的项目， 我们这里选择 `自由风格`， 并且输入项目名称

![编自由风格](http://img.nixiaolei.com/2019-03-10-10-59-00.png)

创建好项目后就会自动进入 当前项目的配置文件中


首先我们看到`General`, 这是一些全局的配置， 在这里我们可以写上该项目的描述

![编全局配置](http://img.nixiaolei.com/2019-03-10-11-02-26.png)


`General`的后面几个类目是构建项目阶段几个比较大的步骤和流程


如果你要配置的“github”项目， 可以在`Git`这里打上勾

并且填写上仓库地址 及 分支 即可， 如果需要填写账户密码， 则可在`	Credentials` 处新增配置


![编github项目](http://img.nixiaolei.com/2019-03-10-11-11-35.png)


接下来是看看构建触发器， 这里的填写根据你的工程需要， 

可以配置定时任务 或 多任务构建

> 本次演示我并没有用到

![编触发器](http://img.nixiaolei.com/2019-03-10-13-26-16.png)



现在来到我们的重点`构建` ， 

我们可以添加步骤，我们可以看到能够支持 `Node`、`shell` 、`批处理`等等方式

这些添加的步骤会按顺序执行， 

![编构建](http://img.nixiaolei.com/2019-03-10-13-30-56.png)


我们需要`shell` 脚本来运行npm 命令

所以我创建了 `shell`的操作步骤

![编shell脚本](http://img.nixiaolei.com/2019-03-10-13-35-35.png)



基于此， `jenkins` 就会自动帮我们做好 测试与打包的步骤，

在此之后， 我们需要将测试通过的项目代码推送到远程发布服务器上， 所以， 我们添加一个`send files or execute commands over SSH` 步骤， 


![编overSSH](http://img.nixiaolei.com/2019-03-16-13-05-58.png)


建议新旧文件搞两个文件夹存放， 这样可以先删除掉旧文件再把新文件拷贝过去

> deploy文件参考

![编deploy文件参考](http://img.nixiaolei.com/2019-03-16-13-08-32.png)



## 你可能遇到的问题

### 命令无效或不存在
> 这里拿npm举例，  其他命令如:node ,都是同理

![编无法使用npm命令](http://img.nixiaolei.com/2019-03-16-12-54-06.png)


因为全局的命令需要设置`软连接` Jenkins 才可以访问的到

如何设置:

先查看一下你的 npm 在哪
> whereis npm

![编查看npm位置](http://img.nixiaolei.com/2019-03-16-12-57-08.png)

这个`/usr/local/bin/npm` 就是你的npm所在位置 ( 使用带 `local` 的地址)

我们来创建 `软连接`, 这里使用刚刚获取到的npm 

> ln -s /usr/local/bin/npm   /usr/bin
 

如果出现无法修改的情况， 那我们就将他强制更改 😄

> ln -sf /usr/local/bin/npm   /usr/bin


再去构建一次Jenkins 任务

看到控制台输出， 现在npm 可以正常运行了

![编npm运行成功](http://img.nixiaolei.com/2019-03-10-18-56-51.png)






## 持续集成的必要条件
1. 必须有github 、svn或私有git服务器
2. 完整的项目
  1. test
  2. 接口测试
  3. 其他的测试内容
3. CI 平台
4. 项目代码要提交到Github、 svn等服务器上
5. 保证代码在开发机的正常
  1. bug
  2. 运行环境 ( 引用的包要写入packa)
6. 保证jenkins 环境一切正常
7. 发布服务器上也要存在基础的运行环境(不包括项目本身引用的包)
8. 必要的静态资源服务器
9. 配置jenkins的自动化处理流程




参考文章:
> https://www.cnblogs.com/loveyouyou616/p/8714544.html

