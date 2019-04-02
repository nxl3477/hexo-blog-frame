---
title: 【Linux】理解权限命令-chown
date: 2019-03-22 20:38:50
categories:
tags:
---

```Python
# 更改文件属组
chgrp [-R] 属组名  文件名

# 更改文件属主 或 属组
chown [-R] 属主名 文件名
chown [-R] 属主名：属组名 文件名

# 修改文件权限 9 个属性
# 有两种设置方式， 一种是数字 一种是符号
# 基本身份 owner/group/others 的各自三种权限
# read(r 4)/write(w 2)/execute(x 1): -rwxr-xr-- = [4+2+1][4+0+1][4+0+0]=754
chmod [-R] xyz 文件或目录

# 符号修改权限
chmod u(user)        +(加入)    r 文件或目录
      g(group)       -(减去)    w 
      o(others)      =(设定)    x
      a(all 全部身份)      

chmod u=rwx,g=rx, o=r 文件名
chmod a-x test1
```


## 图解
![chmod命令](http://img.nixiaolei.com/2019-04-02-20-47-10.png)















