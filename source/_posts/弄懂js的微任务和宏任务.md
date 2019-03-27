---
title: 弄懂js的微任务和宏任务
date: 2019-03-26 23:20:40
categories: JavaScript
tags: JavaScript
---


首先我们都知道JavaScript是单线程的， 当然现在浏览器中 HTML5 为我们提供了`Web-Worker`, 但JavaScript本质上还是一门单线程的语言，

这也是为了保持JavaScript 的简单易学，并且这也和DOM有关，因为如果 JavaScript 是多线程的， 一个县城增加了 DOM，一个删除了DOM， 那该听哪个线程的

所以JavaScript 放弃了多线程



## Eventloop

单线程当遇到比较费事的操作带来的后果就是浏览器卡死， 那怎么办？
为了应对这些问题， JavaScript将事件分为了

* 同步任务
* 异步任务



所有的事件任务都被加入到栈内,  栈的特点是先入后出， 如下图

![js事件执行栈](http://img.nixiaolei.com/js-stack.gif)

看图中， 最开始调用的函数被压在了最下面， 直到最后才出栈

当我们递归书写不规范时， 就会撑爆函数的执行栈， 也就是`爆栈`


## 微任务与宏任务的区别

我们可以把事件处理机制想象成银行柜台的柜员， 柜员每次只能处理接待一个客户， 就和JS一样是单线程的， 每一次的事件循环就是一个来办理业务的客户， 这个客户就是宏任务， 

当前的客户结束之后， 柜员叫号，继续接待下一个客户， 也就是开始下一个宏任务

能进入任务队列的都是已完成的异步操作， 当你注册了异步事件，但还未完成， 就好比你取了排队的票子， 但是人离开了，有可能先去吃饭，有可能先去逛街， 于是当轮到你的号码时， 银行柜员会先跳过你， 当你回来时， 你依旧需要重新排队

在宏任务期间， 可以追加微任务的， 或者说微任务是可以插队的，  就像在柜台办理业务中， 一位大爷在办完自己的本来的业务后，  柜员通常都会问还有什么需要办理的业务， 这时老大爷想了一下：“最近P2P爆雷有点儿多，是不是要选择稳一些的理财呢”，然后告诉柜员说，要办一些理财的业务，这时候柜员肯定不能告诉老大爷说：“您再上后边取个号去，重新排队”。 

所以本来快轮到你来办理业务，会因为老大爷临时添加的“理财业务”而往后推。 
也许老大爷在办完理财以后还想 再办一个信用卡？或者 再买点儿纪念币？ 
无论是什么需求，只要是柜员能够帮她办理的，都会在处理你的业务之前来做这些事情，这些都可以认为是微任务。

所以
**在当前的微任务没有执行完成时，是不会执行下一个宏任务的**

### 宏任务
| API        | 浏览器    |  Node  |
| --------   | :-----:   | :----: |
| I/O      | ✅     |  ✅  |
| setTimeout        | ✅     |   ✅    |
| setInterval        | ✅      |  ✅  |
| setImmediate        |❌     |  ✅    |
| requestAnimationFrame        | ✅     |  ❌  |


有些地方会列出来UI Rendering，说这个也是宏任务，可是在读了HTML规范文档以后，发现这很显然是和微任务平行的一个操作步骤 

HTML规范文档:
> https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model


requestAnimationFrame姑且也算是宏任务吧，requestAnimationFrame在MDN的定义为，下次页面重绘前所执行的操作，而重绘也是作为宏任务的一个步骤来存在的，且该步骤晚于微任务的执行

MDN:
> https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame



### 微任务例子
| API        | 浏览器    |  Node  |
| --------   | :-----:   | :----: |
| process.nextTick      | ❌     |  ✅  |
| MutationObserver        | ✅     |  ❌   |
| Promise.then catch finally       | ✅      |  ✅  |



参考文献
* https://segmentfault.com/a/1190000016022069








