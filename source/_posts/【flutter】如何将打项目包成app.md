---
title: 【flutter】如何将打项目包成app
date: 2019-11-23 10:02:45
categories: flutter
tags: [flutter]
---


# 首先需要创建一个 keystore

```shell
sudo keytool -genkey -v -keystore ~/key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias key
```

设置一下密钥库口令， 自己要记住不要忘记， 最少6位字符（对安卓一窍不通的我刚开始还以为是以前某个时候生成的， 完全想不起来是啥， 后来才知道原来是创建一个新的）

![2019-11-23-10-13-04](http://img.nixiaolei.com/2019-11-23-10-13-04.png)


按命令一步一步来就可以创建好了， 顺便记一下 `key.jks `的生成地址， 待会要用

## 配置 key.properties 文件
进入 `flutter_app项目/android/` 目录， 创建一个 `key.properties` 文件, 填入以下信息， 注意要根据自己填的密钥口令来写入

```text
storePassword=123456	秘钥库密码
keyPassword=123456		key密码
keyAlias=key			key别名
storeFile=D:/android/android_work_space/demo/flutter_app/key.jks		key.jks存放路径
```


## 配置 build.gradle

进入 `flutter_app项目/android/app/build.gradle` 目录， 我们需要添加以下配置

```text
def keystorePropertiesFile = rootProject.file("key.properties")
def keystoreProperties = new Properties()
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
```

放在红色框框区域

![2019-11-23-11-27-16](http://img.nixiaolei.com/2019-11-23-11-27-16.png)


接着用这段代码替换掉原本的 `buidTypes`相关代码  并增加了 `signingConfigs` 相关代码

```text
signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile file(keystoreProperties['storeFile'])
        storePassword keystoreProperties['storePassword']
    }
  }
  buildTypes {
    release {
        signingConfig signingConfigs.release
    }
  }
```

![2019-11-23-11-30-43](http://img.nixiaolei.com/2019-11-23-11-30-43.png)


## 授权允许访问互联网

进入 `flutter_app项目/android/app/src/profile/AndroidManifest.xml`

将以下代码复制进去
```text
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
```

![2019-11-23-11-33-35](http://img.nixiaolei.com/2019-11-23-11-33-35.png)


到这里为止， 我亲身实践还没有成功， 因为还有一个配置文件, 需要再做一次上面的事情

我们可以看到整个目录下面有两个相同的`AndroidManifest.xml`文件
![2019-11-23-11-34-58](http://img.nixiaolei.com/2019-11-23-11-34-58.png)

所以我们需要进入 `flutter_app项目/android/app/src/main/AndroidManifest.xml`

把上面的代码同样粘贴进去

![2019-11-23-11-36-02](http://img.nixiaolei.com/2019-11-23-11-36-02.png)

如此大功告成， 可以打包了

## 打包 app 

flutter build apk


