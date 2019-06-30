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












