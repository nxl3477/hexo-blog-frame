---
title: 【React】react生命周期的变化
date: 2019-04-13 09:46:16
categories: React
tags: React
---

`React16`带来了新的生命周期， 为了更好的贴合`fiber`架构， 移除了几个旧的生命周期， 这几个生命周期也确实不太常用

我们先来看看老的生命周期
## react15生命周期
![react15生命周期](http://img.nixiaolei.com/2019-04-13-10-14-55.png)


光看没用呀，记不住，我们来实际看一下它的打印顺序。
初次加载时：
![react15打印生命周期01](http://img.nixiaolei.com/2019-04-13-10-08-51.png)

点击按钮更新组件，使得传入`Props`发生改变，触发子组件的生命周期
![react15打印生命周期02](http://img.nixiaolei.com/2019-04-13-10-19-11.png)



## react16生命周期

![React16生命周期](http://img.nixiaolei.com/2019-04-14-17-50-09.png)



### 废弃的三个生命周期：
* componentWillMount
* componentWillReceiveProps
* componentWillUpdate

如果你在`react16`中还继续使用这几个什么周期， 他就会提示你这是不安全的
![warning](http://img.nixiaolei.com/2019-04-14-19-02-00.png)


### 新增的两个生命周期:
> 为了配合`fiber`
* getDerivedStateFromProps
* getSnapshotBeforeUpdate


我这个人说话比较简单， 我就简介的说一下这两个生命周期主要的功能及解决的问题， 具体的长篇大论不好描述，也不是我擅长的事情， 我在下面给出了两份不错的文章， 方便自己回忆细节， 也方便大家理解

#### getDerivedStateFromProps
> 用来取代`componentWillReceiveProps`, 是一个静态方法

> 接收两个参数 nextProps, preProps
是一个将接收到的`props`映射到`state`的方法
具体映射规则根据该方法的返回值，

比如，如果这样，返回一个`name`
```Js
static getDerivedStateFromProps(nextProps, prevProps) {
  return { name: "张三" }
}
```

随即我们打印一下`state`
![映射props](http://img.nixiaolei.com/2019-04-14-21-19-29.png)
我们发现，返回的`name` 确实出现在了`state`, 尽管这是我创造出来的， 而不是`props`上传入的

如果我们不想它映射到`state`, 我们可以返回一个`null`

![不映射props](http://img.nixiaolei.com/2019-04-14-21-22-06.png)


根据这个特性利用传入的`nextProps, prevProps`两个参数， 我们就可以做类似 `shouldComponentUpdate`的操作

#### getSnapshotBeforeUpdate
> 该方法获取上一次`render`时的镜像

> 接收 prevProps, prevState 两个参数

新的`getSnapshotBeforeUpdate`生命周期在更新之前被调用（例如，在DOM被更新之前）。此生命周期的返回值将作为第三个参数传递给`componentDidUpdate`。 （这个生命周期不是经常需要的，但可以用于在恢复期间手动保存滚动位置的情况。）

与`componentDidUpdate`一起，这个新的生命周期将覆盖旧版`componentWillUpdate`的所有用例。

来看一下每次的打印结果
```Js
getSnapshotBeforeUpdate(prevProps, prevState) {
  console.log(`---第${times++}次renders---`)
  console.log('prevProps', prevProps)
  console.log('prevState', prevState)
}
```

![查看镜像](http://img.nixiaolei.com/2019-04-14-21-41-52.png)




关于这两个生命周期， 有两个个比较好的文章：
* [理论型文章](https://blog.csdn.net/nnxxyy1111/article/details/80832525#%E4%BB%80%E4%B9%88%E6%98%AFmemoization)
* [使用型文章](https://www.jianshu.com/p/50fe3fb9f7c3)







参考文献:
> https://www.jianshu.com/p/50fe3fb9f7c3
> https://blog.csdn.net/nnxxyy1111/article/details/80832525#%E4%BB%80%E4%B9%88%E6%98%AFmemoization
> https://www.zcfy.cc/article/update-on-async-rendering




