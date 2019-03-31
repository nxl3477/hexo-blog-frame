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





## 💥源码分析
Vue的完全版源码有很多判断以及其他的逻辑， 对于观看源码的人，会造成极大的困难，

因此准备了这版仿照Vue流程实现的 实现了双向绑定的[简版Vue](https://github.com/nxl3477/note/tree/master/Javascript/Vue/%E5%AE%9E%E7%8E%B0vue), 方便学习理解

通过这版对[Vue源码的简易翻版](https://github.com/nxl3477/note/tree/master/Javascript/Vue/%E5%AE%9E%E7%8E%B0vue)， 我们来快速理解Vue原理

### ✨ new Vue

首先看一下，我们的 new Vue, 这是所有操作的入口

```JavaScript
new Vue({
  data: {
    nickname: '张三',
    email: "123123@qq.com"
  },
  el: '#app'
})
```
相信使用过`Vue `的小伙伴都明白， 这里定义了一个`data`， 用于存放变量， el是目标`dom`的选择器

### 🔥 new Vue 执行时做了什么

```JavaScript
function Vue(option) {
  var data = option.data 
  this.data = data
  // 挂载 getter 和 setter
  observe(data, this)
  var id = option.el
  // 编译 模板
  var dom = new Compile(document.querySelector(id), this)
  // 把编译好的模板挂载到 #app 上
  document.querySelector(id).appendChild(dom)
}
```

我们可以看到， Vue其实是一个构造函数， 它接收了一个参数`option`， 这个`option`就是我们`new Vue`传入的那个对象
因此我们可以通过`option` 拿到`data`和`el`两个变量， 当然这是js基础哈， 我就不再说了

拿到`data`后， 我们可以看到它调用了一个 `observe`方法, 将data和this传入( 此时this时Vue实例 )

接下来又根据`el`来获取`dom`， 同样的将获取到的`dom`和`this`传入了 `Compile`中，  并且还接收了一个返回值， 然后又将这个返回值挂到了 `#app`上

是不是感觉， 什么鬼？, 这顿操作是啥

首先看一下疑惑的 `observe`， 传入了 `data`， `this`, 然后就没动静了， 既然如此， 我们就进入`observe`看看

找到`Observe`的构造函数
```JavaScript
function observe(obj, vm) {
  Object.keys(obj).forEach(key => {
    defineReactive(vm, key, obj[key])
  })
}
```
可以看到，它接收一个`obj`和 `vm`， 哦， 这里就一一对应上了， 就是我们刚才传入的 `data`和`this` 

来看看它做了什么， 

它把`obj`枚举了一遍， 并将每一次的  `vm`, `key`,`value` 都传入一个叫 `defineReactive`的方法


好，那就让来看`defineReactive`做了什么

```JavaScript
function defineReactive(vm, key, val) {
  // 为每个变量分配一个 dep实例
  var dep = new Dep()
  // 配置getter和setter并且挂载到vm上
  Object.defineProperty(vm, key, {
    get() {
      if ( Dep.target ) {
        // JS的浏览器单线程特性， 保证整个全局变量在同一时间内， 只有一个监听器使用
        dep.addSub(Dep.target)
      }
      return val
    },
    set(newVal) {
      if ( newVal == val ) return;
      val = newVal;
      // 作为发布者发出通知
      dep.notify()
    }
  })
}
```

我们看到， 它`new`了一个`Dep`， 这个`Dep`就是报刊亭大爷的电话本， 用来收集所有想买报纸或杂志的人的电话， 等到到货时就好挨个通知

然后我们看到了灵魂函数 `Object.defineProperty`,  

嗷那我们应该就明白了， 这里的一顿操作就是为了给`data`里的每个属性都挂载上 `getter`，`setter`, 并且将这些属性直接转移到了`vm`上（Vue实例）

那既然如此， 

🤪让我们看看 `getter`方法做了什么， 

首先它判断了一下`Dep.target`，  如果`Dep.target`为`true` , 就调用`dep`的`addSub`方法， 这里`Dep.target`是啥我们先不管， 留个印象即可

然后它直接`return`了`val`

😵再来看看`setter`方法

`setter`方法接收一个新值，  首先就是判断了新值和原本的值是否相等， 如果相等就不做处理了， 如果不相等， 它将新值赋给`val`

然后调用`dep`实例上的`notify`方法， `notify` 看着名字也知道是通知， 也就是大爷挨个打电话的一个操作


好, 这一块我们理顺了， 是为了挂上`getter`和`setter`， 但又遇到了新问题`dep`， `dep`到底在干什么， 为什么被`getter`，`setter`都使用了 


找到`Dep`的构造函数
```JavaScript
function Dep() {
  // 存放watcher
  this.subs = []
}

Dep.prototype = {
  // 添加watcher, 也就是添加订阅
  addSub(sub) {
    this.subs.push(sub)
  },
  // 通知所有watcher
  notify() {
    this.subs.forEach(sub => {
      sub.update()
    })
  }
}
```

我们可以看到， `Dep`构造函数中维护了一个 `subs`数组， 并且下面的在`prototype`上定义了几个方法， `addSub` 和`notify`，
这不就是刚刚`observe`里调用的两个方法吗， 
哦，明白了， `addSub`原来是将`getter`中传入的 `Dep.target`追加到每个`Dep`实例都单独维护的一个`subs`数组中呀， `notify`就是遍历整个数组，挨个调用`update`方法（先不管update的具体实现）


好， 解决了`observe`方法，那我们就回到最初的`Vue`构造函数中,  继续往下走, 攻克剩余的绿色区域

![Compile](http://img.nixiaolei.com/2019-03-31-11-36-41.png)


我们可以看到，它通过`el` 获取到了`dom`, 并在`new Compile` 将`dom` 传入

那我们就找到`Compile`的构造函数一探究竟
```JavaScript
function Compile(node, vm) {
  if(node) {
    this.$frag = this.nodeToFragment(node, vm)
    return this.$frag
  }
}
```

可以看到， 它接收了一个`node`， 和一个`vm` , 并且判断了一下`node`是否存在， 

并将`node`和`vm`，传入了`this.nodeToFragment`方法， 又将其的返回结果`return`出去， 也就是`new Compile`之后返回的值 ，如下

![Compile02](http://img.nixiaolei.com/2019-03-31-11-49-23.png)


那`this.nodeToFragment`这个方法做了什么， 让我们找到他
```JavaScript
Compile.prototype = {
  nodeToFragment(node, vm) {
    var _this = this
    // 创建文档片段
    var frag = document.createDocumentFragment()
    var child;
    while ( child = node.firstChild ) {
      // 替换变量
      _this.compileElement(child, vm)
      // 剪贴子元素
      frag.append(child)
    }
    return frag
  },
  compileElement(node, vm) {
    var reg = /\{\{(.*)\}\}/;
    // 节点类型为元素, 根据nodeType来判断
    if ( node.nodeType === 1 ) {
      // 获取自定义属性
      var attr = node.attributes
      for (var i = 0; i < attr.length; i++) {
        if (attr[i].nodeName == "v-model") {
          // 获取v-model 绑定的属性名
          var name = attr[i].nodeValue
          // 双向绑定
          node.addEventListener('input', function(e) {
            // 给相应的data属性赋值， 进而触发该属性的set方法
            // 再批处理渲染元素
            vm[name] = e.target.value 
          })
          // 把this ，节点， 还有v-model绑定的变量交给watcher
          new Watcher(vm, node, name, "value")
        }
      }
    }

    // 节点类型为text
    if ( node.nodeType === 3 ) {
      if ( reg.test(node.nodeValue) ) {
        var name = RegExp.$1; // 获取匹配到的字符串
        name = name.trim()
         // 把this ，节点， 还有{{}}中使用的变量交给watcher
        new Watcher(vm, node, name, 'nodeValue')
      }
    }
  }
}
```

我们看到它在`Compile`原型上挂了`nodeToFragment`, `compileElement`两个方法，  `nodeToFragment`方法接收 `node`, `vm`参数

先保存了一下`this`指向,  然后使用`document.createDocumentFragment()`方法创建了一个文档片段， 并将在`while`循环中传入的`node`节点的第一个元素赋值给 `child`变量，
然后使用`compileElement(child, vm)` 将`child`和`vm` 传入, 然后将`child` 追加给创建好的文档片段`frag`, 你肯定会觉得这是个死循环, 其实不是的， 这个`append`对`dom`有剪切的效果，
所以他会一直抽离`node`的第一个节点，直至`node`空了， 吸干他

完成了这顿操作后， 再将`frag`文档片段返回

然后我们来看看它在`while`中调用的`compileElement`方法做了什么

它同样接收`node`和`vm` , 首先就是定义一个正则， 这是用来匹配`{{}}`双括号的， 也就是我们平时的变量写法

然后它判断了一下这个 `node`的节点类型,  如果`nodeType == 1`, 那就说明是元素，  如果`nodeType == 3` 那就说明节点类型是`text`
      
如果节点类型是元素， 就利用`attributes` 方法，获取到该元素身上的属性,  查看是否存在`v-model`这样一个属性， 如果有，就获取到`v-model`填写的变量，交给变量`name`,
然后监听该元素的`input`事件， 

所以每当改元素发生`input`时间时，就将元素上的`value`根据`v-model`上获取到的`name`作为`vm`的`key`去修改`vm`实例上的对应的值， 因为`vm`上的变量已经被挂载此来触发`vm`

最后还创建了一个`Watcher`实例,  传入`vm, node ,name, "value"`这几个参数，

`Watcher`的具体实现我们待会去看

接下来就是判断`node.nodeType == 3`， 也就是text类型的节点， 如果是此类节点， 就先用正则去匹配一下`{{}}`语法， 看看有没有使用到某个变量， 
如果匹配到了， 则通过`RegExp.$1`获取到被匹配到的值， 然后去除左右的空格， 交给变量`name`
最后，同样的创建了一个`Watcher`实例,  传入`vm, node ,name, "value"`这几个参数，

出现两次`Watcher`， 什么情况， 到底干了啥
那， 现在就来让我们看看神秘的`Watcher`构造函数

找到`Watcher`的构造函数
```JavaScript
let uid = 0;

function Watcher(vm, node, name, type) {
  // 单例， 使用原因未知
  Dep.target = this
  // 姓名
  this.name = name;
  // 呵呵哒 uid
  this.id = ++uid;
  // 与变量相关的Node节点
  this.node = node;
  // vm 实例
  this.vm = vm;
  // 变量类型  nodeValue  || value
  this.type = type;
  // 触发自己原型上的update方法
  this.update()
  // Watcher 实例创建结束就把单例置空
  Dep.target = null
}
```

此时我们发现了一个关键的东西`Dep.target` ， 这个鬼东西原来在这里， 它被赋值为了`Watcher`的实例， 然后在`Watcher`实例上挂载了`name`，也就是用到的变量， 还使用了一个`uid`， 不过这`uid`也是呵呵了，用数字作为`uid`, `Vue`的真实源码就这么干的， 为每个`Watcher`都配分一个`uid`， 这会造成数组空间的不连续， 引发内存泄漏

接着说， 然后他将传入的`node`节点， `vm`实例， 还有`type`( 'nodeValue' 和 'value' ), 都挂到了实例上面， 并且还在调用了`update`方法后， 将`Dep.target`设为`null`

那我们来看下`update`做了啥

```JavaScript
Watcher.prototype = {
  update() {
    this.get()
    if(!batcher) {
      // bastcher 单例
      batcher = new Batcher()
    }
    // 加入队列
    batcher.push(this)
  },
  // 获取新值挂到自己的实例上
  get() {
    this.value = this.vm[this.name]  // 触发getter
  }
}
```

看到`update`方法， 首先调用了一下`get`方法， 这个`get`呢就是根据`this.name`从 `vm`实例上取一次值， 并挂到`Watcher`实例上的`value`属性上, 并且他还会触发一次`getter`方法，将自己加入到`dep`中， 也就是加入到报刊亭大爷的电话本中， 便于之后的通知

然后判断了一下`window.batcher`是否存在， 如果不存在就创建一个， 保证其是一个单例模式,
如果存在， 就将自己(`watcher`实例)，通过`push`方法传入

看到这里，又晕了， 什么时候又冒出来一个`Batcher`

我们又找到`Batcher`的构造函数好好分析下，
```JavaScript
// 批处理构造函数
function Batcher() {
  //  重置  has  queue waiting
  this.reset()
}

Batcher.prototype.reset = function () {
  this.has = {}
  this.queue = []
  this.waiting = false
} 

// 将watcher 添加到队列中
Batcher.prototype.push = function (job) {
  let id = job.id
  // 先根据 对象的key 看看是否已经有了这个watcher
  if (!this.has[id]) {
    // console.log(batcher)
    this.queue.push(job)
    // 将watcher 的key的设为true
    this.has[id] = true

    // 延迟执行
    if (!this.waiting ) {
      this.waiting = true
      if ( "Promise" in window ) {
        Promise.resolve().then(() => {
          this.flush()
        })
      } else {
        setTimeout(() => {
          this.flush()
        }, 0)
      }
    }
  }
}


// 执行并情况事件队列
Batcher.prototype.flush = function() {
  this.queue.forEach(job => {
    job.cb()
  })
  this.reset()
}


```

`Batcher`的构造函数很简单， 就调用了一下自己的`reset`方法， 但好像事情远没有这么简单，我们不是在 `Watcher`的`update`方法中调用了`batcher.push`吗， 我也可以在这原型上找的这个方法， 首先它接收一个`job`参数， 也就是`Watcher`实例， 

获取到该`watcher`的`id`, 然后使用这个`id`,去`has`这个对象上访问一下， 看看是否存在，
如果不存在，在证明之前没有添加进来过， 然后将该`watcher`实例加到`queue`队列中， 
并将`has`对象中`id`对应的值设为`true`, 以防止重复加入队列

并且判断一下`waiting`，得知当前是否处于等待状态， 如果不是， 就将`waiting`改为`true`, 然后就是判断当前浏览器的支持情况， 将处理的任务扔到异步队列中，

它这里这么做是为了，只批处理一次， 你一瞬间加入多个`watcher`, 很容易造成重复执行， 利用`Watcher`的`id`来过滤， 并且利用异步， 等你要加的`watcher`都加完了， 我再给你统一的去执行所有`Watcher`

也就是异步任务结束后调用的`flush`方法， 它在内部会遍历`queue`队列， 挨个的调用`Watcher`的`cb`方法
在这一切都执行完成之后， 又调用了一次`reset`方法， 将`bascher`的三个属性重置为初始状态

此时关注点又回到了`Watcher`身上， 它的`cb`方法又做了什么
```JavaScript
Watcher.prototype = {
  // ...省略其他方法

  // 给dom赋值
  cb() {
    // 最终实际虚拟dom 处理结果， 只处理一次
    // 虚拟dom -> diff( 虚拟dom ) -> 局部更新 -> createElement(vNode) -> render
    this.node[this.type] = this.value
  },
}
```

可以看到`cb`方法做的事情很简单那， 就是**根据元素的值类型去修改元素对象的值**， 而这个`this.value`早在之前调用 `Watcher`的`get`方法时就被赋上了

到这里，整个流程就走完了， 相信你还是一头雾水， 我们把整个流程来串一下

1. new Vue
2. 将`data`中的值挂上 `getter`和`setter` 的相应方法， 然后暂且搁置，因为此时还无人调用`getter`和`setter`
3. 通过 `Compile`解析模板， 挨个递归`#app`下的`dom`, 判断元素类型， 如果是元素，并且使用了`v-model`， 就绑定一个`input`事件,  如果是文本类型节点,就去匹配是使用了`{{}}`语法， 最后为他们都创建了一个`watcher`
4. 每个`watcher` 用来保存相关的元素对象， `vm`实例，使用的`变量` 以及元素值类型, 并将自己的实例交给， `Dep.target`， 并触发自己的`update`方法，`update`方法又会调用`get`方法， `get`方法又会触发该变量的`getter`， 这也就使得`getter`中可以将该`watcher`放入`dep`实例中， 最后将自己也放入`Bacher`中，用以批处理以及将`Dep.target`置空
5. `Batcher`是个单例， 根据`Watcher`的`id`, 它用来过滤重复传入的`Watcher`, 保证一个`Watcher`只触发一次, 并将更新事件丢入异步，等当前的连续操作执行完成后去调用`Watcher`的`cb`方法更新`dom`
6. 之后用户修改了变量, `setter`又会调用`dep`这个发布者来发出通知， 相关的`Watcher`的`update`方法再次被调用， 又会加入`batcher` , `batcher`等待异步完成后又调用`Watcher`的`cb`方法更新`dom`


**到这里就整个串完了，但是感觉废话还是有点多， 再简化一点流程**:

new Vue --> `Observe` 挂载 `setter` 和 `getter` -->  `Compile` 编译模板 --> 为每个指令分配一个`watcher` --> 创建时会调用一次`watcher.update` 将自己加入到`batcher`的队列 -->
并且此时会触发 `getter` 将`watcher`加入`dep` -->  `batcher` 统一来处理`watcher`后初始化自己 -->  当用户修改某个变量时 --> `dep`通知`watcher` --> `watcher`又被加入`batcher`处理 --> `watcher` 更新`dom`


😕好了， 神秘的`Vue`源码已被揭开面纱， 但这仅仅是简易版的实现， 真实的`Vue`非常庞大， 还有更多的内容， 这里只是让大家明白`MVVM`的核心原理


项目源码:
> https://github.com/nxl3477/note/tree/master/Javascript/Vue/%E5%AE%9E%E7%8E%B0vue

优质文献: 
* [汤姆大叔的观察者模式](http://www.cnblogs.com/TomXu/archive/2012/03/02/2355128.html)