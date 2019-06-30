---
title: 【JS】MessageChannel消息通道
date: 2019-06-30 21:49:45
categories: JavaScript
tags: [JavaScript]
---

## 介绍
宏任务! 点对点之间的通信

Channel Messaging API的Channel Messaging接口允许我们创建一个新的消息通道，并通过它的两个MessagePort 属性发送数据。


它会返回两个端口 `Port1`和 `Port2`

![2019-06-30-21-51-18](http://img.nixiaolei.com/2019-06-30-21-51-18.png)



## 使用场景思考
1. Vue 中用作与 $nextTick 的备选方案,  是宏任务
2. 用作与Fiber 的进程调度中
3. 可以用在组件之间的通信
4. 利用参数传递默认深拷贝的特性来做深拷贝.
5. 与Iframe 通信



## 一段Demo

```JS
var channel = new MessageChannel();
var para = document.querySelector('p');
    
var ifr = document.querySelector('iframe');
var otherWindow = ifr.contentWindow;

ifr.addEventListener("load", iframeLoaded, false);
    
function iframeLoaded() {
  otherWindow.postMessage('Hello from the main page!', '*', [channel.port2]);
}

channel.port1.onmessage = handleMessage;
function handleMessage(e) {
  para.innerHTML = e.data;
}
```







