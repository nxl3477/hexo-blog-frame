---
title: 【记录】jest进行单元测试时提示router-view 未注册报错了
date: 2019-09-10 19:56:36
categories: 记录
tags: 记录
---



当我想要克隆一个项目时， 出了点小意外， 使用https 使用非常顺畅， 但是换成 ssh的方式就出问题了。死活行不通

git clone  一直提示这样的错误
```
Cloning into 'xxx'...
zsh:1: command not found: connect
ssh_exchange_identification: Connection closed by remote host
fatal: Could not read from remote repository.
```

或是这样的错误:
```
Cloning into 'xxx'
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```



不止配置了一遍gitHub 的 `ssh key` 敢确信绝对没问题， 寻遍整个互联网依旧没有找到答案.




后来我发现原因时因为： 我创建的 ssh公钥没有被系统所使用, 因为默认只有is_rsa, 如果你自定义了那就需要添加

需要添加一下你创建的公钥， 告诉系统可以使用

```
ssh-add -K ~/.ssh/你的ssh key名称(不带.pub)
```

比如：
```
ssh-add -K ~/.ssh/github_rsa
```


于是， clone 顺利


附上创建密钥的命令:
> ssh-keygen -t rsa -C "your_email@youremail.com"




参考文献:
> http://shinancao.cn/2016/12/18/Programming-Git-1/









