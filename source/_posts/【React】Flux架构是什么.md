---
title: 【React】Flux架构是什么
date: 2019-04-16 21:30:19
categories: React
tags: React
---

为了避免`React`的`state`混乱， 让`React`专注于`ui`,  `Flux`就应运而生了

Flux是一种架构思想， 专门解决软件的结构问题。他跟MVC架构是同一类东西， 但是更加简单和清晰。

## Flux构成
* `View`视图层 
* `Action`(动作): 视图层发出的消息(比如mouseClick)
* `Dispatcher`(派发器): 用来接收`Actions`, 执行回调函数
* `Store`(数据层): 用来存放应用的状态， 一旦发生变动， 就提醒`Views`要更新页面

![Flux构成](http://img.nixiaolei.com/2019-04-16-22-00-28.png)

Flux 的最大特点，就是数据的"单向流动"。

1. 用户访问 View
2. View 发出用户的 Action
3. Dispatcher 收到 Action，要求 Store 进行相应的更新
4. Store 更新后，发出一个"change"事件
5. View 收到"change"事件后，更新页面

上面过程中，数据总是"单向流动"，任何相邻的部分都不会发生数据的"双向流动"。这保证了流程的清晰。



## Flux与Redux
因为`Flux`的不好理解， 所以社区做出了很多的努力， 常用的`Redux`就是`Flux`的其中一种实现










优质文献
> [阮一峰的flux](https://www.cnblogs.com/fliu/articles/5245923.html)


