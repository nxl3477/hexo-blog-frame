---
title: 【工程化】了解webpack原理
date: 2019-07-11 22:58:30
categories: 工程化
tags:  [webpack, 工程化, Ast]
---


> webpack 的成功之处在于其loader , 但 webpack 的失败之处也出在这个loader

对于webpack 来讲， 万物都是模块， webpack 为什么可以做到万物都可以 `require`就是使用了loader,  但同时也是loader 导致了webpack 的编译速度（你懂的）， 这是为什么呢， 这里先说一下基本原理
***

webpack 在处理的时候， 会经过多个loader, 比如`babel-loader`,`less-loader`， 那如何把这些看似毫不相关的， 又是天南地北找来的， 出自不同开发者之手的loader 集合起来呢:

首先是第一个接受任务的loader, loader1 会接受代码的字符串， 然后把代码变成 Ast 进行处理， 结束以后再转为 string字符串。

也就是说，整个 loader1 经历了一下的流程:

```
字符串 -> Ast 静态语法树 -> 遍历Ast处理业务 -> 字符串
```

同样的loader2 也会经历这样一个过程！


也就是说所有loader 穿起来就是:
```
loader1 开始 -> 获取字符串 ->  Ast -> 遍历Ast处理业务 
-> 字符串 -> loader1结束 -> loader2 开始 -> 获得字符串 
-> Ast -> 遍历Ast处理业务 -> 字符串 -> loader 2 结束 
-> loader3开始 -> ………等等
```

乍一看， 你可能会觉得， 是不是太蠢了， 为什么不等到全部loader处理完成再转换回 字符串， 还要转来转去， 原来打包时间都在这消耗掉了。
没错， 你当然不是第一个想到的， 这就是之前大火的parcel 的打包机制（虽然parcel的零配置很优秀， 但因为被webpack抄走了， 所以也渐渐势头不在）,  并且parcel还启用了多核打包:

```
string字符串 -> Ast(loader1 & loader2 & loader3) -> string 字符串  外加多核打包📦
```

一气呵成， 这感觉，多爽， 确实比webpack快多了 ，省了那么多步骤， 能不快吗


## webpack 与优化
上面提到了 parcel 的多核打包， 可我们的主场是webpack ,怎么能不提一提webpack 的多核打包呢， 

相信大家都听过或者尝试过臭名昭著的 `happypack` 多核打包插件， 但这个插件实际上在高版本的webpack 上效果略低啊

happypack 可以用，但也需要考虑下应用场景： 在需要打包的文件太少不建议， 因为多核打包也是需要消耗系统性能的， 反而会使打包变慢了

实际操作起来效果不明显


## 如何实现一个loader

 熟话说， 看源码是最好的学习方式， 现在我们先找一个比较简单的开源loader ,看看别人是怎么写的?
 
 我们下载`markdon-loader`, 打开node_modules 中其的代码， 发现如下：
 ![2019-09-14-19-30-43](http://img.nixiaolei.com/2019-09-14-19-30-43.png)


让我们加点注释理解一下

![2019-09-14-19-34-46](http://img.nixiaolei.com/2019-09-14-19-34-46.png)


loader 有一个前置钩子， 会在进入主体函数前被调用：

```js
module.exports = function (content, map, meta) {
  //  this 是我们运行时数据调用方法和补充载体 也就是loader函数的执行上下文， 所以可以通过webpack 提供的函
  // 数库来从 this 获取外部 rule 处对 loader 配置的option

  console.log('🍎进入loader')
  console.log('前置钩子内容🍌', this.data)
  return content + ";console.log(1)"
}

// 一个叫pitch的前置钩子 ( 在进入⬆️主体前触发)
module.exports.pitch = function (r, prerequest, data) {
  console.log("进入前置钩子")
  data.value = "are you ok"
}
```

在外面webpack 配置的地方引用我们上方编写的loader
```js
const path = require('path')

module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: path.resolve("./loader/index.js"),
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  }
}
```


让我们看下此时的输出结果
![2019-09-14-20-18-39](http://img.nixiaolei.com/2019-09-14-20-18-39.png)


#### 获取相关的option 配置
webpack 提供的函数库可以帮助我们， 在执行阶段从 this 对象上获取到我们配置时所传入的 option 的相关 loader  配置
```js
const loaderUtils = require('loader-utils')

module.exports = function (content, map, meta) {
  //  this 是我们运行时数据调用方法和补充载体 也就是loader函数的执行上下文， 所以可以通过webpack 提供的函
  // 数库来从 this 获取外部 rule 处对 loader 配置的option

  console.log('🍎进入loader')
  console.log('前置钩子内容🍌', this.data)
  const options = loaderUtils.getOptions(this)
  console.log('🍊获取到的配置文件', options)
  return content + ";console.log(1)"
}

// 一个叫pitch的前置钩子 ( 在进入⬆️主体前触发)
module.exports.pitch = function (r, prerequest, data) {
  console.log("进入前置钩子")
  data.value = "are you ok"
}
```


此时我们就成功获取到了配置时所传入的参数了

![2019-09-14-20-24-56](http://img.nixiaolei.com/2019-09-14-20-24-56.png)

#### 异步loader
如果我们的编写过程中有异步的需求, 我们可以使用下面这两个方法

* this.async 执行异步函数
* this.callback 执行函数的回调， 返回处理结果



## loader  与 Ast
既然loader处理的核心是 Ast, 那么我们同样的夜来尝试一下Ast 的使用,

首先我们下载一个`acorn` , 这是一个专门处理 ast 的库, 我们使用 acorn 提供的方法尝试去转换一段代码， 然后使用walk 所提供的方法将其转换回来
```JS
const acorn = require("acorn")
const walk = require('acorn-walk')

console.log(acorn.parse("const a = 20"))
walk.simple(acorn.parse('let x = 10'), {
  Literal(node) {
    console.log(`Found a literal: ${node.value}`)
  }
})
```

输出的结果

![2019-09-14-21-07-23](http://img.nixiaolei.com/2019-09-14-21-07-23.png)

