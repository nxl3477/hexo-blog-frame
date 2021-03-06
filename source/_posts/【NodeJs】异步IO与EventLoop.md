---
title: 【NodeJs】异步IO与EventLoop
date: 2019-03-26 22:49:27
categories: NodeJs
tags: [ NodeJs, JavaScript ]
---

IO密集型就是需要频繁请求的程序，高访问量，  CPU密集型就是需要进行大量计算的 


## 异步IO的好处

* 前端通过异步IO可以消除UI堵塞。
* 假设请求资源A的时间为M,请求资源B的时间为N.那么同步的请求耗时为M+N.如果采用异步方式占用时间为Max(M,N)。
* 随着业务的复杂，会引入分布式系统，时间会线性的增加，M+N+...和Max(M,N…)，这会放大同步和异步之间的差异。
* I/O是昂贵的，分布式I/O是更昂贵的。
  * 分布式因为你读取到了这个值， 但并没有在你这台机器上， 远程的机器想要读取到那就必须要走网络， 这个过程就变长了
* NodeJS 适用于IO密集型不适用CPU密集型




## Node对异步IO的实现
> 完美的异步IO应该是应该是应用程序发起非阻塞调用，无需通过遍历或者事件幻想等方式轮询。

Node.js 的事件循环和在浏览器中的实现不太一样， 在Node.js 中事件循环交给了Libuv托管，  Libuv 和 v8 是分别独立的线程， 辅助Node.js 进行事件轮询

Node.js 依靠v8运行javascript, 并且绑定了底层的操作系统

当V8接收到用户的请求进入， 通过绑定系统接口，将事件交给Libuv处理， Libuv 将事件加入事件队列( event queue)， EventLoop 不行的旋转并执行同步事件，当遇到异步事件时，将其交给 worker Threads 线程， 比如操作文件就是交给工作线程去同步完成， 当文件操作完毕后， 根据文件描述符去获取数据， 然后将结果作为参数返回给事件队列中等候的回调函数


![EventLoop](http://img.nixiaolei.com/2019-03-29-22-50-40.png)



## 常用的Node控制异步技术手段
> Promise 是一个语法， 规范， js只是实现了这个规范，  Promise 是一个高级接口， 创建一次，不容易修改， then就是 低级接口， 可以一直then ,处理逻辑

1. Step、wind（提供等待的异步库）、Bigpipe、Q.js
2. Async、Await
3. Promise/Defferred 是一种先执行异步调用，延迟传递的处理方式。Promise是高级接口，事件是低级接口。低级接口可以构建更多复杂的场景，高级接口一旦定义，不太容易变化，不再有低级接口的灵活性，但对于解决问题非常有效
4. 由于Node基于V8的原因，目前还不支持协程。协程不是进程或线程，其执行过程更类似于子例程，或者说不带返回值的函数调用。一个程序可以包含多个协程，可以对比与一个进程包含多个线程，因而下面我们来比较协程和线程。我们知道多个线程相对独立，有自己的上下文，切换受系统控制；而协程也相对独立，有自己的上下文，但是其切换由自己控制，由当前协程切换到其他协程由当前协程来控制。



##  底层的知识
1. CPU时钟周期：1/cpu主频 -> 1s/3.1 GHz
![底层知识](http://img.nixiaolei.com/2019-03-29-22-54-03.png)


3. 操作系统对计算机进行了抽象，将所有输入输出设备抽象为文件。内核在进行文件I/O操作时，通过文件描述符进行管理。应用程序如果需要进行IO需要打开文件描述符，在进行文件和数据的读写。异步IO不带数据直接返回，要获取数据还需要通过文件描述符再次读取。

![文件操作](http://img.nixiaolei.com/2019-03-29-22-54-36.png)


