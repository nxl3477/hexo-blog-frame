---
title: 【Vue】源码解析
date: 2019-03-18 09:50:34
categories: Vue
tags: Vue
---

现在三大框架风起云涌， `JQuery`老大哥的光辉不再， 使前端成为了各路诸侯的兵家必争之地， 

当然作为一名优质的前端，光跟风学框架是肯定不行的， 要知其然而知其所以然

要了解`MVVM`的本质原理， `virtual Dom`和 `Diff`算法解决的问题

拒绝盲目跟风


## 🍎diffDom优劣
现在很多人都说 `Vue`, `React` 多牛， `Diff`算法快，不用操作`Dom`。

只能无语。

`Diff`算法不是不需要操作`Dom`,  而是不需要开发者去操作`Dom`了， `Diff`算法其实不快， 就算使用了`virtual Dom`,  还得花实现把真实`Dom` 转换为 `virtual Dom` 再去比对， 这远远没有`js`直接 `getElementById`直达目标来的快

那Diff算法不快为什么还要用呢?

`Diff`算法其实是给那些比较随意的新手开发者准备的

看这个例子

```HTML
<!-- 原本的dom -->
<ul id="ul">
  <li>a</li>
  <li>a</li>
  <li>a</li>
</ul>
```
```JavaScript
// 拿到结果不管三七二十一把旧dom全替换了
$.get('/api', (res) => {
  var _HTML = ""
  for(var i=0; i< res.length; i++){
    _HTML = "<li>" + res[i] +"</li>"
  }
  $("#ul").html(_HTML)
})
```

如果是原始的`Dom`操作， 有很多小白会像这个例子一样， 不管`Dom`需不需要更新， 他都把`ajax`返回的请求全部跑一边，生成`HTML`模板， 然后把原本的所有`li`都删了，  再把新的模板放进去， `Dom`少还看不出来， 如果`Dom`多了呢， 上千的`Dom`， 这顿操作一下就玩炸了。

况且网站优化原则就是尽量减小`Dom`操作， 如果是有经验的开发者， 会选择找到有变化的位置，使用`append`插入



## 📄Vue架构目录
去[Vue官网](https://github.com/vuejs/vue)下载`Vue`源码看看，

打开里面会有一个 `src`目录， 里面就是整个的`Vue`源码

目前有6个目录， 作用分别如下

![Vue目录](http://img.nixiaolei.com/2019-03-30-10-44-35.png)


`Vue.js` 的组成是由 `core` + 对应的 '平台' 补充代码构成（独立构建和运行时构建只是 `platforms` 下 `web` 平台的两种选择）

`Vue`的核心原理就在`core`文件夹中， 让我们进入 `core` 文件夹看看

![core文件夹](http://img.nixiaolei.com/2019-03-30-10-57-26.png)

了解了目录，接下来我们就来研究`Vue`的双向绑定

## 🔗双向绑定（响应式原理） 所涉及到的技术
* ✅ **Obejct.defineProperty** _【提供getter 和 setter】_
* ✅ **Observer** _【提供getter 和 setter】_
* ✅ **watcher**  _【提供getter 和 setter】_
* ✅ **Dep** _【负责收集watcher】_
* ✅ **Directive** _【处理Vue模板指令】_


### Obejct.defineProperty


`Obejct.defineProperty` 是整个`Vue`的灵魂， 

来看一下`Obejct.defineProperty` 如何使用
```JavaScript
var obj = {}
var c;
Object.defineProperty(obj, 'a', {
  get() {
    console.log('getter')
    return a
  },
  set(newVal) {
    console.log('setter')
    c = newVal
    this.a = newVal
  }
})

obj.a = '234'
console.log(c) // 234
console.log(obj.a) // 234
```


它帮助`Vue`实现了双向绑定, 但也因为这个， `Vue`也只能舍弃了对低版本浏览器的支持。


![defineProperty兼容](http://img.nixiaolei.com/2019-03-30-11-48-41.png)

它只能兼容到`IE9` ， 并且市面上的`polyfill`实现的也并不是很好

那低版本如何代替`Obejct.defineProperty` ， 难道真没了它就不行吗？


**当然有**：

1. 👆 > IE 7

实际上在`IE7`的时候就已经有暴露了 `__defineGetter__` 方法，
![__defineGetter__](http://img.nixiaolei.com/2019-03-30-12-57-56.png)

具体用法如下
```JavaScript
var random = {};
random.__defineGetter__('ten', function() { 
    return Math.floor(Math.random()*10); });
random.__defineGetter__('hundred', function() { 
    return Math.floor(Math.random()*100); });

random.ten // 随机的一个值
```

2.  👇 < IE 7

早年间的`IE` 是支持`VBScript`,  [VBScript](http://www.w3school.com.cn/vbscript/index.asp) 就可以直接写类， 并且也支持`get`，`set`方法

```JavaScript
class Test {
  get name () {
    
  }
  set name() {

  }
}
```

### 🐶霸道的IE
说了这么多`IE`的坏， 这里也带一嘴`IE`的好，

`IE`能够调用`EXE`程序, 比如`JS`无法设置打印机的宽高，就可以利用`ActiveObjectX`来做到, 甚至可以修改`word`格式等等  , 所以办公类的项目离不开`IE`





## 😕MVVM 双向数据绑定流程


MVVM: Model–view–viewmodel

那怎么区分这些层呢

* 🚀**Model**: `Observer`
* ✈️**view** : `directive`
* 🚚**viewmodel**: `Watcher` && `Dep`  _【用于连接 Model 和 view】_


![双向数据绑定](http://img.nixiaolei.com/2019-03-30-11-51-42.png)



先看`Directive`, 这就是我们平时写的`vue`指令， 如上面举例得的`v-text="times"`, 这就是一个指令， 一个`Directive`会分配一个`Watcher`



### Observer

观察者模式是软件设计模式的一种。在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实时事件处理系统。订阅者模式涉及三个对象：发布者、主题对象、订阅者，三个对象间的是一对多的关系，每当主题对象状态发生改变时，其相关依赖对象都会得到通知，并被自动更新。

简单的描述就是:

你想买漫画， 但是问了报刊亭的大爷， 大爷说现在没有， 还没到货， 然后你回去了， 第二天你又去问， 大爷还是说没有， 如果你每天这样问， 大爷估计会嫌你烦。 如果这时候你把你的电话给大爷， 大爷记录到他的**本子**上,  当大爷的漫画到货的时候电话通知你。

这时你就是`订阅者`, 大爷就是`发布者`, 你们就存在一个发布订阅者的关系



**Vue 中的Observer**

Observer会观察两种类型的数据，`Object` 与 `Array`
对于`Array`类型的数据，由于 `JavaScript` 的限制， `Vue` 不能检测变化,会先重写操作数组的原型方法，重写后能达到两个目的，

当数组发生变化时，触发 `notify` 如果是 `push`，`unshift`，`splice` 这些添加新元素的操作，则会使用`observer`观察新添加的数据重写完原型方法后，遍历拿到数组中的每个数据 使用`observer`观察它而对于`Object`类型的数据，则遍历它的每个`key`，使用 `defineProperty` 设置 `getter` 和 `setter`，当触发`getter`的时候，`observer`则开始收集依赖，而触发`setter`的时候，`observer`则触发`notify`。

#### 对 Object 的处理
`Observer` 对象的标志就是`__ob__` 这个属性，这个属性保存了 `Observer` 对象自己本身。对象在转化为 `Observer` 对象的过程中是一个递归的过程，对象的子元素如果是对象或数组的话，也会转化为 `Observer` 对象


#### 对数组的处理

其实 `observeArray` 方法就是对数组进行遍历，递归调用 `observe` 方法，最终都会走入 `walk` 方监控单个元素。而 `walk` 方法就是遍历对象，结合 defineReactive 方法递归将属性转化为 `getter` 和 `setter`






### Watcher
`Watcher` 是将模板和 `Observer` 对象结合在一起的纽带。`Watcher` 是订阅者模式中的订阅者。`Watcher` 的两个参数： `expOrFn` 最终会被转换为 `getter` 函数， cb 是更新时执行的回调。依赖收集的入口就是get函数。

> getter 函数是用来连接监控属性与 `Watcher` 的关键


只有通过`watcher` 触发的getter 会收集依赖，而所谓的被收集的依赖就是当前`watcher`.初始化时传入的参数 expOrFn 中涉及到的每一项数据，然后触发该数据项的 getter 函数；getter 函数中就是通过判断 Dep.target的有无来判断是 `Watcher` 初始化时调用的还是普通数据读取，如果有则进行依赖收集




### Dep

这个方法是在响应式的过程中调用的，用户修改数据触发 setter 函数，函数的最后一行就是调用 dep.notify 去通知订阅者更新视图。

### Directive

![Directive](http://img.nixiaolei.com/2019-03-30-14-50-38.png)


关于编译这块vue分了两种类型，一种是文本节点，一种是元素节点



vue内置了这么多的指令，这些指令都会抛出两个接口bind 和 update，这两个接口的作用是，编译的最后一步是执行所有用到的指令的bind方法，而 update 方法则是当watcher 触发 update 时，Directive会触发指令的update方法


observe -> 触发setter -> watcher -> 触发update -> Directive -> 触发update -> 指令












## Vue3重写的原因
// `Obejct.defineProperty`



优质文献: 
* [汤姆大叔的观察者模式](http://www.cnblogs.com/TomXu/archive/2012/03/02/2355128.html)