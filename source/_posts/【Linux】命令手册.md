---
title: 【Linux】命令手册
date: 2019-04-02 20:35:33
categories: Linux
tags: Linux
---


linux 只有区的概念

## 终端快捷键
| 命令 | 功能 | 
| ------ | ------ | 
| ctrl + c | 结束正在运行的程序 【 ping 、 teInet等】 | 
| ctrl + d | 结束输入或退出shell |
| ctrl + s |  暂停屏幕输出 |
| ctrl + q |  恢复屏幕输出 |
| ctrl + l |  清屏， 等同于Clear |
| ctrl + a / ctrl + e | 快速移动光标到行首 / 行尾 |



## 身份命令
| 命令 | 功能 | 
| ------ | ------ | 
| sudo | 暂时以root 用户身份运行命令 | 
| su | 切换用户身份, root身份切换不需要密码 |
| exit |  退出当前身份或退出连接 |
| w | 查看正在登录用户|

### 路径知识
* Linux下文件前带`.`表示隐藏文件
* 一个`.`代表当前目录, 两个`..`代表上级目录


## 资源目录相关命令
> a是显示隐藏文件, l是显示长列表格

命令 | 说明
---|---
ls 或 dir | 查看当前目录内容
ls -l  | 显示长格式目录, 有创建时间,修改信息, 文件大小
ls -a | 显示隐藏文件
ls -al | 查看目录所有对应的权限( )
pwd | 宣誓当前目录的全部路径
cd ~ | 进入到当前分配`home`目录下的用户区域, 比如`/home/nxl`
cd / | 进入系统的根目录


## 文件命令
* [old] 表示旧文件       
* [new] 表示操作后的文件     
* [target] 目标文件        
* [targetDir] 目标目录

命令 | 说明
---|---
mkdir | 创建文件夹
rmdir | 删除文件
touch [new] | 创建文件
rm [target] | 删除文件
rm -r [targetDir] | 删除文件夹
rm -rf * | 删除所有内容(r递归, f强制)
cp [old] [test/new] | 复制文件
cp [old] -R [test/new] | 复制目录 
ln -s old new  | 建立软链


## 界面切换命令 init [number]
命令 | 说明
---|---
0  | 关机
1 | 单用户
2 | 多用户状态没有网络服务
3 | 多用户状态有网络服务(就是平时的小黑窗)
4 |  系统未使用保留给用户
5 | 图形界面
6 | 系统重启

## 服务 / 进程管理命令

命令 | 说明
---|---
systemctl  | 查看进程
ststemctl [ status , start , stop , restart ] nginx |  如对 nginx 执行相关命令 
top  | 进程资源实时状态
ps  [aux] | 进程快照
kill [-9] pid | 杀死进程
pkill [name] | 根据进程名杀死进程




## 网络管理命令
命令 | 说明
---|---
ifconfig| 查看网络接口（ interface）
ip | 网络配置工具箱
route | 诊断网络
 ipdown eth0 | 关闭`eth0`网卡
ipup eth0 |开启`eth0`网卡
ss -anp , grep :22 |  查找占用端口情况
netstat -anp , grep :22 |  查找占用端口情况


## 命令行下载命令
命令 | 说明
---|---
curl| 查看网络接口（ interface）
wget | 网络配置工具箱
wget -c | 断点续传


## vi / vim 行编辑器
> vim是vi的增强版

命令 | 说明
---|---
vi [target] | 编辑目标文件
cat [target] | 查看文件内容
i (insert) | 在当前光标所在字符的前面，转为编辑模式；
a (append) | 在当前光标所在字符的后面，转为编辑模式；
o| 在当前光标所在行的下方，新建一行，并转为输入模式；
I|在当前光标所在行的行首，转换为输入模式
A|在当前光标所在行的行尾，转换为输入模式
O|在当前光标所在行的上方，新建一行，并转为输入模式；
esc | 退出编辑, ubuntu 需要 `esc + :`
:q| 退出编辑
:q!| 强制退出
:wq| 退出并保存


## 系统操作命令
命令 | 说明
---|---
！| 强制执行命令
shutdown -h now  | 立刻关机
shutdown -r now /reboot | 立刻重启计算机
su - root | 先用普通账户登录再切换 sudo临时已管理员操作
logout | 用户注销

## 帮助命令
命令 | 说明
---|---
--help  | 查看命令帮助
man xxx | 查看详细手册

## 辅助命令
命令 | 说明
---|---
0  | 关机












