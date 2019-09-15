---
title: 【工程化】webpack5不完全尝鲜
date: 2019-09-15 17:40:18
categories: 工程化
tags:  [webpack, 工程化]
---




## 如何安装webpack 5
> npm install webpack@next -D



## 📦打包一下
我们还是去尝试打包这样一段代码
![2019-09-15-17-44-55](http://img.nixiaolei.com/2019-09-15-17-44-55.png)

最终生成
```js
/******/ ((modules, runtime) => { // webpackBootstrap
/******/ 	"use strict";
/******/ 	// The module cache
/******/ 	var installedModules = {};
/******/
/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {
/******/
/******/ 		// Check if module is in cache
/******/ 		if(installedModules[moduleId]) {
/******/ 			return installedModules[moduleId].exports;
/******/ 		}
/******/ 		// Create a new module (and put it into the cache)
/******/ 		var module = installedModules[moduleId] = {
/******/ 			i: moduleId,
/******/ 			l: false,
/******/ 			exports: {}
/******/ 		};
/******/
/******/ 		// Execute the module function
/******/ 		modules[moduleId](module, module.exports, __webpack_require__);
/******/
/******/ 		// Flag the module as loaded
/******/ 		module.l = true;
/******/
/******/ 		// Return the exports of the module
/******/ 		return module.exports;
/******/ 	}
/******/
/******/
/******/
/******/ 	// the startup function
/******/ 	function startup() {
/******/ 		// Load entry module and return exports
/******/ 		return __webpack_require__("./src/index.js");
/******/ 	};
/******/
/******/ 	// run startup
/******/ 	return startup();
/******/ })
/************************************************************************/
/******/ ({

/***/ "./src/index.js":
/*!**********************!*\
  !*** ./src/index.js ***!
  \**********************/
/*! exports [maybe provided (runtime-defined)] [no usage info] */
/*! runtime requirements:  */
/***/ (() => {

eval("console.log(\"你好，webpack\")\n\n//# sourceURL=webpack:///./src/index.js?");

/***/ })

/******/ });
```

是不是比webpack4 少了好多
> webpack4 具体情况可以参考 站内文章 `【工程化】webpack-编译后文件解析`

少去了一大坨的恶心代码！

这是因为：

> webpack5放弃了对兼容做处理的相关代码， 转而交给用户去做， 以此将其代码量减少为之前的三分之一不到


## 固定的id
> 天下苦moduleIds 和 chunkIds 已久

在webpack4 的时候， 我们的异步 chunkId 和moduleId 一直是一个大问题， 为什么这么说呢， 

在此之前的各种 id 如下: 

![2019-09-15-22-12-09](http://img.nixiaolei.com/2019-09-15-22-12-09.png)

natural , size 一般会用在我们的动态引入的异步组件上， natural 是自然数， size 是尺寸大小， 一般动态文件都会变成这样: `0.js`, `1.js`,  看似很完美的东西, 实际上， 会带来一些问题

1. 不管是 natural 还是 size , 当一个组件模块被改变时就会影响到其他的模块的id,  比如: 一开始有a.js , b.js, c.js 对应 0.js , 1.js ,2.js  ,但是后来我们不需要a.js 了， 那现在就变成了: b.js , c.js 对应 0.js , 1.js。
2. 1⃣️所说的id 所带来的改变， 随之而来的影响就是造成网站缓存的报废， 也就是说之前用户辛苦加载的缓存没有用了， 并且是全部报废， 一个文件的改动影响到全部， 整个代价太大了。 
3. 更有，甚至可能会造成文件的引用错误， 因为之前0.js 对应的是 a.js , 1.js 对应的 b.js , 现在变了 0.js 对应了 b.js, 1.js 对应了 c.js, 浏览器直接走缓存 获取到这份js 文件， 就是引错文件了， 那项目不久炸了


在webpack4 时期的曲线救国方案：

> 使用魔法注释来告诉webpack 这个异步组件应该叫什么名字

这确实是一个解决办法， 但是很麻烦， 大点的项目动则上百个组件， 一个一个维护成本实在有点大， 而且后期的修改都得注意， 所以， 虽然有用， 但是不好用


### webpack5 的福音
> 该方法的原理是通过分析文件， 来产生的简版md5

webpack5 带来了一个新的chunkId 的实现方式 `deterministic` , 

```js
module.exports = {
  optimization:{
    chunkIds: "deterministic”,
    moduleIds: "deterministic"   
  }
}
```
只需要这样， 就可以生成针对该文件的唯一chunkId, 太棒啦

![2019-09-15-22-35-45](http://img.nixiaolei.com/2019-09-15-22-35-45.png)


## 更好的缓存

如下当使用持久缓存时，不再需要cache-loader。 对于 babel cacheDirectory 等也是如此。

```js
module.exports = {
    cache: {
        type: "filesystem"
    }
}
```


参考文献:
> https://juejin.im/post/5c8761925188251a0844d3a4