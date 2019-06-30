---
title: 【React】Fiber架构
date: 2019-06-30 01:06:56
categories: React
tags: [React, Fiber]
---
## Fiber 是什么

Fiber 是一个名词， 在计算机科学中除了进程（Process） 和线程（Thread）的概念还有一个概念叫做Fiber, 英文含义就是“纤维”， 意指比Thread更细的线， 也就是比线程（Thread）控制得更精密的并发处理机制。Fiber可以提升复杂React应用的可响应性和性能。 Fiber 即是React 新的调度算法（ reconciliation algorithm） 现有React 中， 更新过程是同步的，当组件树比较庞大的时候， 浏览器主线程被React占用。用户此时输入Input 会造成页面卡顿。

Fiber使用协作式多任务处理任务。 将原来的整个Virtual Dom 的更新任务拆分成一个个小的任务。 每次做完一个小任务之后， 放弃一下自己的执行将线程空闲出来， 看看有没有其他的任务。如果有的话， 就暂停本次仍无， 执行其他的任务， 如果没有的话，就继续下一个任务。

## React16与Fiber
1. react16升级了reconcile 算法架构， 从stack升级为fiber 架构。每次组件的state更新都会出发reconcile的执行也是一个递归过程， 而且一开始知道递归完所有节点才停止， 因此称为stack算法。由于js是单线程的， 因此此时就可能会影响用户的输入或者Ui的渲染帧频造成卡顿， 降低用户体验。而fiber 架构则不一样， 底层是基于 requestdleCallback来调度diff算法的执行， 它的特点顾名思义就是利用空闲时间来完成任务。 注意这里的空闲时间就是相对于那些优先级更高的任务（比如用户输入、 Ui渲染）来说的。

2. requestIdelCallback Polyfill 使用了一个33毫秒， 这个时间是React 认为的一个可以接收的最大值， 如果运行设备能做到大与30fps， 那么它会去调整这个值（通常情况下可以调整到16.6ms）。 调整策略是用当前每帧的总时间与实际每帧的时间进行比较， 当实际时间小于当前时间且稳定( 前后两次都小于当前时间 )， 那么就会认为这个值是有效的， 然后将没帧时间调整为该值（取前后两次中时间大的值）， 还有就是requestAnimationFrame 回调的第一个参数， 每一帧的起始时间， 最终借助requestAnimationFrame 让一批扁平任务恰好控制在一块一块的33ms这样的时间片内执行即可












