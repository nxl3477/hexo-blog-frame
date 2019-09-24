---
title: 【React】为什么React 的方法需要bind
date: 2019-09-24 19:47:43
categories: React
tags: [React]
---

今天下班的时候， 同事问我React 的中的方法为什么需要箭头函数包裹， 或者是bind 绑定一下this指向， 才能被调用， 我当时也是一愣啊， 虽然以前好像也去研究过这个问题， 但好像答案也并不是那么明确。 于是我就把当时我的想法跟他阐述了一下， 当时我是这么说的： 
> this 在初始化时是指向的对象实例， 但调用方法的时候不是， 因为jsx不是真正的dom 它会被转化为虚拟dom, 所以事件是通过react代理的， 并且这个涉及到javascript的作用域问题， 正常的变量都会是词法作用域， 也就是在书写阶段就能确定该变量的作用域， 而this 则是一个怪咖，他是一个动态作用域， 它是在执行期间才会确定 this 指向， 俗话说就是谁调用就指向谁。 因此，事件被react 代理了注册了， 如果是正常情况的this 在调用期间自然无法获得原本的class 指向， 至于为什么 this 是undefind ， 这是因为 this 应该是指向到了 window 上， 而执行react 的代码中开启了严格模式所以 this 就变成了undefind

上面这些， 咋一看好像没什么问题， 但我仔细一想好像又不太对劲， 问题真的是出在 React 代理注册事件的时候吗

让我们来一探究竟：

![2019-09-24-20-14-45](http://img.nixiaolei.com/2019-09-24-20-14-45.png)


这是React 官网的一段话， 大致意思是（用的翻译工具🐶）:
> 你必须要小心的这JSX回调的this。 在JavaScript中，类方法不是默认绑定的。 如果你忘了绑定this.handleClick并把它传递到的onClick，这将是不确定的，当函数实际被调用的时候。

> 这不是React 的行为; 这是在JavaScript中的函数工作的原理的一部分。 通常，如果指的是方法，无需（）后，如onClick = {this.handleClick}，则应该bind 指向。

> 如果调用bind惹恼了你，可以使用箭头函数

也就是说问题不是出在了React 身上， 而是出在了javascript 自身的执行机制上。 那让我们回归本质， 看看到底是怎么一回事吧。

让我们来看同样一段代码， 不同情况下的this指向。


```js
var demo = {
  fn(key) {
    console.log(key, this)
  }
}

demo.fn('1')
// 这一步就类似我们在jsx中的事件赋值操作
var dFn = demo.fn
dFn('2')

var arrowsFn = () => demo.fn('3')
arrowsFn()
```

![2019-09-24-20-25-26](http://img.nixiaolei.com/2019-09-24-20-25-26.png)



我们可以看到 ，2 的赋值方式导致了this指向的丢失， React 的事件挂载时我们也是如此操作， 这也就说明了是js 本身的问题而非 React 导致的， 尽管React 没有做相应的处理也有责任


让我们继续， 不是说React 中指向的是 undefind 吗， 那我们打开严格模式看看是不是如此: 

```js
"use strict";
var demo = {
  fn(key) {
    console.log(key, this)
  }
}

demo.fn('1')
var dFn = demo.fn
dFn('2')

var arrowsFn = () => demo.fn('3')
arrowsFn()
```

果然如此， this 指向变为了undefind

![2019-09-24-20-30-04](http://img.nixiaolei.com/2019-09-24-20-30-04.png)


