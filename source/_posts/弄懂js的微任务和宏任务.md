---
title: 弄懂js的微任务和宏任务
date: 2019-03-26 23:20:40
categories: JavaScript
tags: JavaScript
---


首先我们都知道JavaScript是单线程的， 当然现在浏览器中 HTML5 为我们提供了`Web-Worker`, 但JavaScript本质上还是一门单线程的语言，

这也是为了保持JavaScript 的简单易学，并且这也和DOM有关，因为如果 JavaScript 是多线程的， 一个县城增加了 DOM，一个删除了DOM， 那该听哪个线程的

所以JavaScript 放弃了多线程



## javascript事件循环

单线程当遇到比较费事的操作带来的后果就是浏览器卡死， 那怎么办？
为了应对这些问题， JavaScript将事件分为了

* 同步任务
* 异步任务



所有的事件任务都被加入到栈内,  栈的特点是先入后出， 如下图

![js事件执行栈](https://img.nixiaolei.com/js-stack.gif)

看图中， 最开始调用的函数被压在了最下面， 直到最后才出栈

digui


为了实现异步，也就有了独特的JavaScript事件循环



参考文献
* https://segmentfault.com/a/1190000016022069








