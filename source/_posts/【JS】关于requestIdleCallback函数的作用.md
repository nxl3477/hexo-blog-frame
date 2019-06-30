---
title: 【JS】关于requestIdleCallback函数的作用
date: 2019-06-30 12:25:42
categories: JavaScript
tags: JavaScript
---


requestIdleCallback 会在浏览器每一帧的空闲时期依次调用函数， 

如果说， 一个浏览器的效率很高，有60fps的刷新频率， 也就是说该浏览器每一帧的刷新时间是16.6s， 如果说电脑比较卡， 那刷新频率可能就会下降， 会是 30fps(原则上30fps是肉眼能接收的最低频率)。 此时，每一帧也就是33.3s， 该函数的出发场景就是在帧与帧之间， 也可以是说帧的末尾， 实际上每一帧并不一定会完全消耗完 16.6s 甚至是 33.3s， 他会有剩余的时间留给浏览器去执行js，  requestIdleCallback 就是如此， 用它调用的函数会在浏览器的该帧期间，渲染过后的剩余时间内调用该回调，

这也是react 16 fiber架构的调度算法核心


## 基本语法
> https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestIdleCallback


![2019-06-30-12-36-16](http://img.nixiaolei.com/2019-06-30-12-36-16.png)


并且我们还可以通过 `deadline.timeRemaining`方法来查看该帧期间的剩余时间


## Demo
首先尝试搭建一个最简单的demo ,打印一下 deadline 、剩余时间 和 该函数的返回值
```Js
const id = window.requestIdleCallback((deadline) => {
  // 剩余时间， 单位ms, 指的是该帧的剩余时间
  console.log(deadline.timeRemaining())
  // 表示该帧里面没有执行回调， 超时了
  console.log(deadline)
}, {
  // 1. 即超时时间， 不提供的话浏览器会自己去算
  // 2. 如果给定 timeout ， 那到了时间， 都会立刻执行回调 callback
  timeout: 1000
})

console.log(id)
```

返回结果:

![2019-06-30-12-48-25](http://img.nixiaolei.com/2019-06-30-12-48-25.png)

我们可以看到：
1. 该函数的返回结果(id)的值： 1
2. 浏览器本帧的剩余空闲时间: 1.2750000000000001
3. 回调接收的 `deadline`方法， 里面有一个 didTimeout 属性



## 如何取消requestIdleCallback

将 requestIdleCallback 的返回值（一个无符号长整数），可以把它传入 Window.cancelIdleCallback() 方法，来结束回调


## 如何实现 polyfill
requestIdleCallback 这个方法浏览器的兼容相当差， 那怎么办， React16 Fiber中也使用了， 别人是如何解决的呢？

答案是： `requestAnimationFrame`

![2019-06-30-12-59-21](http://img.nixiaolei.com/2019-06-30-12-59-21.png)

利用回调接收的 DOMHighResTimeStamp 参数， 计算出每一帧所花费的时间， 自己去实现一个 `requestIdleCallback` 的polyfill ,  简直完美