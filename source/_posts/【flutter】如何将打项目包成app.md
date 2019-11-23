---
title: 【flutter】如何将打项目包成app
date: 2019-10-23 10:02:45
categories: flutter
tags: [flutter]
---


# 首先需要创建一个 keystore

```shell
sudo keytool -genkey -v -keystore ~/key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias key
```

设置一下密钥库口令， 自己要记住不要忘记， 最少6位字符（对安卓一窍不通的我刚开始还以为是以前某个时候生成的， 完全想不起来是啥， 后来才知道原来是创建一个新的）

![2019-11-23-10-13-04](http://img.nixiaolei.com/2019-11-23-10-13-04.png)












