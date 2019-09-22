---
title: 【PWA】如何使用PWA
date: 2019-09-19 21:53:14
categories: PWA
tags: [ PWA ]
---


## 为什么选择PWA
1. 浏览器的存储机制有缺陷， 
2. indexDB, webSQL 虽有存储空间，但读取性能较弱，  
3. localhost 存储存在上线, 虽能通过iframe 和域名 扩展空间， 但成本较大， 并且较差的手机， 超过2.5M就会出现性能问题，引起卡顿
4. 浏览器实现的mainifest 不完善， 很有可能一份数据在用户手机上存一辈子， 无法更新
5. 以上的方式始终离不开网络
6. 2018年safari 全面支持pwa
7. 增加首屏体验

## pwa 的特点

[ 响应式 ] --- ⽤用户界⾯面可以兼容多种设备，⽐比如: 桌⾯面，移动端，平板。
[ 应⽤化 ] --- 交互体验接近native应⽤用。
[ ⽹络低依赖: ] --- 无⽹络或者低速网络下依然可以使⽤。
[ 延续性 ] 借助推送功能，能够维持⽤户粘性。
[ 可安装 ] 可以被安装到主屏，这样⽤户随时可以从主屏启动应⽤。
[ 可被发现 ] 被视为⼀个独⽴立应⽤，⽽且可以被搜索.
[ 可更新 ] --- 当⽤户重新联⽹网时，可以更新内容.
[ 安全 ] --- 可以使⽤HTTPS来防⽌止内容伪造和中间⼈攻击。
[ 渐进性 ] --- 所有⽤用户都可以使⽤用，浏览器器⽆无关。
[ 兼容url ] --- 可以通过url传播。


## pwa 数据获取流程

![2019-09-19-23-15-01](http://img.nixiaolei.com/2019-09-19-23-15-01.png)

注册成功的service worker , 会在网站运行时拦截住网页的所有请求， 然后向服务器询问是否有需要更新的数据， 如果得知不需要更新就从之前存储的缓存中读取， 大大加快网页加载速度


## pwa 的生命周期
![2019-09-22-21-58-13](http://img.nixiaolei.com/2019-09-22-21-58-13.png)
![2019-09-22-21-56-41](http://img.nixiaolei.com/2019-09-22-21-56-41.png)


生命周期如图所示， 在service  worker 初次加载时， 只会从 install 阶段执行到 activating 

也就是从 安装 -> 激活阶段

在第二次用户访问后， 才会触发fetch 事件

并且，整个service worker 的所有工作都是一个事务的过程， 也就是说， 当有多个service workder 时有一个失败， 所有的service worker 注册都失败


## 如何更好的监控离线

使用offline.js


## 如何编写 service worker

### 注册
在注册service workder 之前先判断一下浏览器是否支持

然后从 `navigator` 中提供的`serviceWorker` 引入'sw.js' 文件，  一般整个文件放在网站根目录

并且我们可以通过一些方法获得注册的相关信息 比如应用范围等等。
```js
<script>
  if( "serviceWorker" in navigator ) {
    console.log("当前 控制权", navigator.serviceWorker.controller)
    // 注册service worker
    navigator.serviceWorker.register('./sw.js').then((registeraction) => {
      // 注册成功
      console.log('serviceWorker注册成功,范围: ',  registeraction.scope)

    }).catch((err) => {
      // 注册失败
      console.log("serviceWorker 注册失败", err)
    })
  }
</script>
```

### 安装
首先创建一个 `sw.js` 文件， 然后编写相对应需要被缓存的文件数组， 也就是 下面的`filesToCahe` ， 还要为你的缓存编写一个类似于版本戳的 `cacheName` ， 
然后在self 对象上挂载`intsall` 事件,  

编写一个方法， 将需要缓存的列表添加到caches 上，  然后用event方法去执行

```js
var cacheName = "nxl-pwa-step-v1"
var filesToCahe = [
  "/css/index.css",
  "/images/cat.png",
  "/index.html",
  "/"
]
// 生命周期， 安装阶段， 首次注册才会触发
self.addEventListener('install', (event) => {
  // 首次注册被触发
  // 能够缓存所有的应用
  console.log('安装成功')

  // 等待缓存工作完成 
  event.waitUntil(updateStaticCache)
})


// 具体的缓存操作
function updateStaticCache() {
  // 使用 serveice workder 全局参数caches
  return caches.open(cacheName).then((cache) => {
    // 原子事务操作， 一旦某个文件缓存失败 整个缓存全部作废
    return cache.addAll(filesToCahe)
  })
  // 强制 处于wait 状态的脚本 进入激活状态， 当有多个 service workder 注册任务的时候 会变成事务等待， 一旦一个失败之后， 就会全部放弃， 这句话的意思就是放弃等待其他的service workder任务自己执行下去
  .then(() => self.skipWaiting())
}
```

### 监听激活事件
> 同样是sw.js

监听激活事件

并对比之前编写的版本戳， 如果本次的版本戳和之前的不一样， 就把之前的版本戳从缓存中删掉


```js
// 生命周期， 激活阶段
self.addEventListener('activate', function(event) {
  console.log('激活成功')

  // caches 对象中不一定只有一个key , 会有多个key 的情况， 如果有多个就需要遍历， 一般多页面应用就会有多个
  event.waitUntil(caches.keys().then(function(keyList) {
    // 遍历当前版本戳的缓存
    return Promise.all(keyList.map(function(key) {
      // 如果旧的key 不等于 当前的 key 那就把旧的删了， （ 这里只判断了一种key 的情况 ）
      if( key !== cacheName ) {
        return caches.delete(key)
      }
    }))
  }))
})
```


### 拦截请求
> 同sw.js 

网站的每一个请求都会被service worker 拦截住， 我们可以利用 Response 对象去伪造一个服务端的响应， 在这里我们主要是去匹配 caches 对象中的文件是否和发送请求的目标数据相等， 如果相等， 则直接走缓存，如果不想等则放行这个请求， 不影响数据加载

```js
// 资源请求阶段 截取数据请求
// 所有网站请求都会经过它
self.addEventListener('fetch', (event) => {
  // 拦截网络请求
  console.log('拦截请求触发')
  // 伪造响应
  // event.respondWith(new Response("拦截请求"))
  console.log("截取当前网络请求",  event.request, fetch)
  event.respondWith(
    // 去匹配 每次的请求
    caches.match(event.request).then(function (response) {
      // 如果匹配到了则返回匹配结果, 如果没有匹配到，就使用fetch 请求放行
      if( response ) {
        console.log('response 🍊')
        return response
      }else {
        console.log('fetch 🍎')
        return fetch(event.request.url)
      }
    })
  )
})
```



## webpack工程化
* offline-plugin
* Worker-precache-webpack-plugin


![2019-09-22-22-55-18](http://img.nixiaolei.com/2019-09-22-22-55-18.png)

### 更优的方案

利用google 的workderbox ， 当然，这也有工程化的方案, 一句话搞定所有

* workbox-webpack-plugin


官方地址:

> https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin


## 更完善的pwa 解决方案
 
更完善的pwa 解决方案可以使用 lavas ， 这是vue + pwa 的解决方案

![2019-09-22-22-58-17](http://img.nixiaolei.com/2019-09-22-22-58-17.png)




## 全部代码

> index.js 

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>学习Pwa</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="./css/index.css">
  </head>
  <body>
    <h1>PWA测试应用</h1>
    <img src="./images/cat.png" width="200px" alt="">
    <script src="./js/index.js"></script>
    <script>
      if( "serviceWorker" in navigator ) {
        console.log("当前 控制权", navigator.serviceWorker.controller)
        // 注册service worker
        navigator.serviceWorker.register('./sw.js').then((registeraction) => {
          // 注册成功
          console.log('serviceWorker注册成功,范围: ',  registeraction.scope)

        }).catch((err) => {
          // 注册失败
          console.log("serviceWorker 注册失败", err)
        })
      }
    </script>
  </body>
</html>
```


> sw.js
```js
/** 
 * serviceWorker几个重要的参数
 * 1.self 代表了serviceWorker作用域
 * 2.caches 代表了serviceWorker的全局参数
*/

// 添加版本戳
// 所有sw 的值全部以cacheName 为版本戳依据， 当此时的key 和老的key 相同则从老的记录取， 如不一致，则重新安装
var cacheName = "nxl-pwa-step-v1"
var filesToCahe = [
  "/css/index.css",
  "/images/cat.png",
  "/index.html",
  "/"
]
// 生命周期， 安装阶段， 首次注册才会触发
self.addEventListener('install', (event) => {
  // 首次注册被触发
  // 能够缓存所有的应用
  console.log('安装成功')

  // 等待缓存工作完成 
  event.waitUntil(updateStaticCache)
})


// 具体的缓存操作
function updateStaticCache() {
  // 使用 serveice workder 全局参数caches
  return caches.open(cacheName).then((cache) => {
    // 原子事务操作， 一旦某个文件缓存失败 整个缓存全部作废
    return cache.addAll(filesToCahe)
  })
  // 强制 处于wait 状态的脚本 进入激活状态， 当有多个 service workder 注册任务的时候 会变成事务等待， 一旦一个失败之后， 就会全部放弃， 这句话的意思就是放弃等待其他的service workder任务自己执行下去
  .then(() => self.skipWaiting())
}


// 生命周期， 激活阶段
self.addEventListener('activate', function(event) {
  console.log('激活成功')

  // caches 对象中不一定只有一个key , 会有多个key 的情况， 如果有多个就需要遍历， 一般多页面应用就会有多个
  event.waitUntil(caches.keys().then(function(keyList) {
    // 遍历当前版本戳的缓存
    return Promise.all(keyList.map(function(key) {
      // 如果旧的key 不等于 当前的 key 那就把旧的删了， （ 这里只判断了一种key 的情况 ）
      if( key !== cacheName ) {
        return caches.delete(key)
      }
    }))
  }))
})

// 资源请求阶段 截取数据请求
// 所有网站请求都会经过它
self.addEventListener('fetch', (event) => {
  // 拦截网络请求
  console.log('拦截请求触发')
  // 伪造响应
  // event.respondWith(new Response("拦截请求"))
  console.log("截取当前网络请求",  event.request, fetch)
  event.respondWith(
    // 去匹配 每次的请求
    caches.match(event.request).then(function (response) {
      // 如果匹配到了则返回匹配结果, 如果没有匹配到，就使用fetch 请求放行
      if( response ) {
        console.log('response 🍊')
        return response
      }else {
        console.log('fetch 🍎')
        return fetch(event.request.url)
      }
    })
  )
})
```


## 项目地址
> https://github.com/nxl3477/note/tree/master/pwa/code