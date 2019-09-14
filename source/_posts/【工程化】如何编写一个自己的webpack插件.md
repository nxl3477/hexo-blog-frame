---
title: 【工程化】如何编写一个自己的webpack插件
date: 2019-09-14 23:05:23
categories: 工程化
tags:  [webpack, 工程化]
---




## webpack 实现插件的机制
[ 创建 ] -- webpack在其内部对象上创建各种钩子
[ 注册 ] -- 插件将自己的方法注册到对应钩子上， 交给webpack
[ 调用 ] -- webpack 编译过程中， 会适时地触发相应钩子， 因此也就触发了插件的方法 



## 第一个demo
不知道这么起步， 那咱就先去复制一个， 官网提供了一个最最最简易的demo

> https://webpack.js.org/concepts/plugins/

复制下来是这样的:
```js
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
  // 每个插件必备的方法
  apply(compiler) {
    // compiler 是整个webpack编译的核心
    // tap 可以理解为触发订阅
    compiler.hooks.run.tap(pluginName, compilation => {
      console.log('🍎The webpack build process is starting!!!');
    });
  }
}

module.exports = ConsoleLogOnBuildWebpackPlugin;
```

把这个插件引入webpack , 虽然是成功跑起来了啊， 但是一脸蒙蔽， 完全不知道在干嘛也不行啊

首先每个插件都必须要有一个`apply` 方法， 它会接收一个`compiler` ， 这个`compiler` 是所有webpack的核心， 所以webpack 编译相关的内容自然也就在它上面了

接着，我们看到了 complier 上有一个hooks , 后面还有一大串， 那这个`hooks`是哪里来的呢， 怎么回事呢？

让我们去源码里一探究竟：

![2019-09-14-23-39-17](http://img.nixiaolei.com/2019-09-14-23-39-17.png)


哦～ 原来所有 hooks 相关的方法都在这里了， 并且我们发现 compiler 是继承自 `Tapable`

不光如此， 连接受的参数中的 `compilation` 也继承自 Tapable, compilation 代表了当次编译的chunk

![2019-09-15-00-16-21](http://img.nixiaolei.com/2019-09-15-00-16-21.png)

那啥是 Tapable 啊？

## 认识 Tapable
Tapble 是 webpack 的灵魂插件， 它是一个用于做消息通知的包.

多说无用， 让我们来单独玩一下 tapable

```js
const {
  //同步串行的钩子， 不用关心函数的返回值
  SyncHook,
  // 同步串行的钩子 ， 上一个返回值不为null 跳过剩下的逻辑
  SyncBailHook,
  // 同步串行， 上一个值可以传递给下一个值
  SyncWaterfallHook,
  SyncLoopHook,
  AsyncParallelHook,
  AsyncParallelBailHook,
  AsyncSeriesHook,
  AsyncSeriesBailHook,
  AsyncSeriesWaterfallHook 
} = require("tapable");


const queue = new SyncHook(["name", "name2"])
// 订阅
queue.tap("1", function (name, name2) {
  console.log("1⃣️", name, name2)
  return 1
})

queue.tap("2", function (name, name2) {
  console.log("2⃣️", name, name2)
})

queue.tap("3", function (name, name2) {
  console.log("3⃣️", name, name2)
})


// 触发整个钩子
queue.call('webpack', "webpack-cli")
```

此时的输出
![2019-09-15-00-31-59](http://img.nixiaolei.com/2019-09-15-00-31-59.png)

可以看的出 ， 当queue 的call 方法被调用时， 之前挂载的 tap 方法都被触发了， 这其实就是前端里几乎无处不在的观察者模式





##  附上一个之前写的webpack 插件
这是之前用来处理swig模版编译的plugin 
```js
// 1. 何时才能拦截最后生成的swig
// 2. 如何分清这个swig文件对应的Js
class HtmlAfterWebpackPlugin {
  // compiler 是webpack的实例
    apply(compiler) {
      //  此处是实例运行期间， 可以吧 tap 当成是 on
      // 实例上有钩子， 将插件注册到webpack实例钩子上 （run初始运行阶段, compilation: 完成阶段）
        compiler.hooks.compilation.tap(pluginName, compilation => {
          console.log("🍉webpack 构建过程开始！");
          // 因为webpack的版本更新不友好， 所以新的插件挂载方式需要自己从源码中查找
          // 此阶段是 html-webpack-plugin 提供的，  所以必须在 html-webpack-plugin 之后执行
      

          // 此处是将回调挂载到 webpack 执行过程中的某个阶段， 也就是生命周期
          // Plugin的顺序是 从上至下挂钩子， 但执行顺序不一定 
          compilation.hooks.htmlWebpackPluginAfterHtmlProcessing.tap(pluginName, htmlPluginData => {
            let _html = htmlPluginData.html
            const jsResult = assetsHelp(htmlPluginData.assets) 
            // 路径别名
            _html = _html.replace(/views:/g, "../../")
            _html = _html.replace(/components:/g, "../../../components/")
            // 指定位置插入js css
            _html = _html.replace(/<!--injectjs-->/g, jsResult.js.join(""))
            _html = _html.replace(/<!--injectcss-->/g, jsResult.css.join(""))
            // _html = _html.replace("<!--injectjs-->", jsResult.js.join(""))
            htmlPluginData.html = _html
            // htmlPluginData.html = _html
            
          })
        });
    }
}

```