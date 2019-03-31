---
title: JavaScript事件机制
date: 2019-03-26 23:20:40
categories: JavaScript
tags: JavaScript
---


首先我们都知道JavaScript是单线程的， 当然现在浏览器中 HTML5 为我们提供了`Web-Worker`, 但JavaScript本质上还是一门单线程的语言，

这也是为了保持JavaScript 的简单易学，并且这也和DOM有关，因为如果 JavaScript 是多线程的， 一个县城增加了 DOM，一个删除了DOM， 那该听哪个线程的

所以JavaScript 放弃了多线程






## 事件机制

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



### 优先级

不管是微任务还是宏任务， 都是异步任务， 当他们的事件处理完成后， 最终都是要归回到同步事件队列处理的， 也就是主进程， 


既然是主进程，主进程又要负责浏览器的渲染， 那这三者之间的优先级是什么呢


因为微任务实际上是宏任务的其中一个步骤， 也就是下一次的宏任务到来之前必然得先执行完当前的宏任务的微任务, 所以可以这么看:
> 浏览器渲染 > 微任务 > 宏任务

微任务会插队， 插入每一次的执行栈末尾， 宏任务比较可怜， 只要存在微任务，就会被插队，顺序就会往后


***

看下图， 我们可以理解微任务和微任务分别维护着一个队列， 微任务的箭头指向靠前， 表示微任务会优先于宏任务加入事件队列，  宏任务会延迟加入

理论上UI渲染的优先级是比较高的， 但实际上如果你的微任务或宏任务之内， 出现了死循环或大量计算的情况， UI渲染就会被阻塞住， 无法进行，也就是页面卡死状态， 毕竟js单线程


![事件关系](http://img.nixiaolei.com/2019-03-29-22-31-43.png)




## EventLoop

每办理完一个业务，柜员就会问当前的客户，是否还有其他需要办理的业务。_（检查还有没有微任务需要处理）_ 
而客户明确告知说没有事情以后，柜员就去查看后边还有没有等着办理业务的人。_（结束本次宏任务、检查还有没有宏任务需要处理）_ 
这个检查的过程是持续进行的，每完成一个任务都会进行一次，而这样的操作就被称为Event Loop。_(这是个非常简易的描述了，实际上会复杂很多)_

而且就如同上边所说的，一个柜员同一时间只能处理一件事情，即便这些事情是一个客户所提出的，所以可以认为微任务也存在一个队列，大致是这样的一个逻辑：














### 宏任务例子
| API        | 浏览器    |  Node  |
| --------   | :-----:   | :----: |
| I/O      | ✅     |  ✅  |
| setTimeout        | ✅     |   ✅    |
| setInterval        | ✅      |  ✅  |
| setImmediate        |❌     |  ✅    |
| requestAnimationFrame        | ✅     |  ❌  |


I/O这一项感觉有点儿笼统，有太多的东西都可以称之为I/O，点击一次button，上传一个文件，与程序产生交互的这些都可以称之为I/O。


有些地方会列出来UI Rendering，说这个也是宏任务，可是在读了[HTML规范文档](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)以后，发现这很显然是和微任务平行的一个操作步骤 



requestAnimationFrame姑且也算是宏任务吧，requestAnimationFrame在[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)的定义为，下次页面重绘前所执行的操作，而重绘也是作为宏任务的一个步骤来存在的，且该步骤晚于微任务的执行




### 微任务例子
| API        | 浏览器    |  Node  |
| --------   | :-----:   | :----: |
| process.nextTick      | ❌     |  ✅  |
| MutationObserver        | ✅     |  ❌   |
| Promise.then catch finally       | ✅      |  ✅  |
| MessageChannel 消息队列    |      |    |



## 浏览器中的情况

在上边简单的说明了两种任务的差别，以及Event Loop的作用，那么在真实的浏览器中是什么表现呢？ 
首先要明确的一点是，宏任务必然是在微任务之后才执行的（因为微任务实际上是宏任务的其中一个步骤）

I/O这一项感觉有点儿笼统，有太多的东西都可以称之为I/O，点击一次button，上传一个文件，与程序产生交互的这些都可以称之为I/O。

假设有这样的一些DOM结构：
```HTML
<style>
  #outer {
    padding: 20px;
    background: #616161;
  }

  #inner {
    width: 100px;
    height: 100px;
    background: #757575;
  }
</style>
<div id="outer">
  <div id="inner"></div>
</div>
```

```JavaScript
const $inner = document.querySelector('#inner')
const $outer = document.querySelector('#outer')

function handler () {
  console.log('click') // 直接输出

  Promise.resolve().then(_ => console.log('promise')) // 注册微任务

  setTimeout(_ => console.log('timeout')) // 注册宏任务

  requestAnimationFrame(_ => console.log('animationFrame')) // 注册宏任务

  $outer.setAttribute('data-random', Math.random()) // DOM属性修改，触发微任务
}

new MutationObserver(_ => {
  console.log('observer')
}).observe($outer, {
  attributes: true
})

$inner.addEventListener('click', handler)
$outer.addEventListener('click', handler)

```

如果点击#inner，其执行顺序一定是：click -> promise -> observer -> click -> promise -> observer -> animationFrame -> animationFrame -> timeout -> timeout。

因为一次I/O创建了一个宏任务，也就是说在这次任务中会去触发handler。 
按照代码中的注释，在同步的代码已经执行完以后，这时就会去查看是否有微任务可以执行，然后发现了Promise和MutationObserver两个微任务，遂执行之。 
因为click事件会冒泡，所以对应的这次I/O会触发两次handler函数(_一次在inner、一次在outer_)，所以会优先执行冒泡的事件(_早于其他的宏任务_)，也就是说会重复上述的逻辑。 
在执行完同步代码与微任务以后，这时继续向后查找有木有宏任务。 
需要注意的一点是，因为我们触发了setAttribute，实际上修改了DOM的属性，这会导致页面的重绘，而这个set的操作是同步执行的，也就是说requestAnimationFrame的回调会早于setTimeout所执行。


## Node Js中的情况
Node也是单线程，但是在处理Event Loop上与浏览器稍微有些不同，这里是[Node官方文档](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/#event-loop-explained)的地址。


就单从API层面上来理解，Node新增了两个方法可以用来使用：微任务的process.nextTick以及宏任务的setImmediate。


### 几个特殊的API
1. SetTimeout和SetInterval 线程池不参与
2. process.nextTick() 实现类似SetTimeout(function(){},0);每次调用放入队列中，在下一轮循环中取出。
3. setImmediate();比process.nextTick()优先级低


来看看这段代码

```JavaScript
setTimeout(() => {
  console.log(1)
}, 0)

setImmediate(() => {
  console.log(2)
})

process.nextTick(() => {
  console.log(3)
})

new Promise((resolve, reject) => {
  console.log(4)
  resolve(4)
}).then(() => {
  console.log(5)
})

console.log(6)
```

这道题目的答案是 463512

这里有几个注意点， 第一就是 nextTick ，它的优先级比 promise.then 要高， 如下图， 它加入队列的方式属于插队行为, 它直接加入到了当前执行栈（同步队列）与 下一次的等待执行栈之间

![nextTick](http://img.nixiaolei.com/2019-03-29-22-45-02.png)


我们都知道 then 比 setTimeout 优先级高

然后就是 setTimeout 和 setImmediate 的问题了，

setImmediate与setTimeout的区别，在官方文档中的定义，setImmediate为一次Event Loop执行完毕后调用。 setTimeout则是通过计算一个延迟时间后进行执行。

但是在实际使用中， 当setTimeout 设置为0时， 他们都是放在队列最后,  所以执行顺序就是谁先写就先执行谁了， 但当setTimeout 设置的时间大于0 时（最低延迟会被忽略）， 情况就不一样了， setTmmediate 会先被执行

![setImmediate](http://img.nixiaolei.com/2019-03-29-22-45-25.png)




参考文献(原文写的不错， 所以我大部分是复制，方便之后自己回顾理解)
* https://segmentfault.com/a/1190000016022069








