---
title: 【架构】利用systemJs构建微前端
date: 2019-09-17 09:56:53
categories: 架构
tags: [架构, 微前端]
---

微前端，其实就是微服务在前端的一种实践

## 微前端的特性
> 取代 iframe

1. 各个开发团队都可以自行选择技术栈， 不受同一项目中其他团队影响;
2. 各个交付产物都可以被独立使用， 避免和其它交付产物耦合;
3. 各个交付产物中的样式不会污染到其他组件;
4. 各个交付产物都可以自由使用浏览器原生API， 而非要求使用封装后的API;




## 目前实现微前端的多种手段
现阶段实现微前端的方式大致有以下六种：

1. 使用 HTTP 服务器的路由来重定向多个应用
2. 在不同的框架之上设计通讯、加载机制，诸如 Mooa 和 Single-SPA
3. 通过组合多个独立应用、组件来构建一个单体应用
4. iFrame。使用 iFrame 及自定义消息传递机制
5. 使用纯 Web Components 构建应用
6. 结合 Web Components 构建


## 为什么不用 iframe 
[ 不可控制 ] --- iframe 嵌入的显示区大小不容易控制， 存在一定局限性
[ bfcache ] --- URL的记录完全无效， 页面刷新不能够记忆， 刷新会返回首页， iframe 功能之间跳转也无效
[ 兼容性坑 ] --- iframe 的样式显示， 兼容性等都具有局限性
[ 性能开销 ] --- iframe 阻塞onload， 占用连接池， 多层嵌套页面崩溃




## 整体实现思路

利用systemJs 的模块加载能力，略微调整webpack 产出相关配置， 将各个项目按照 systemJs 的模块规则导出， 利用脚本将所有应用整合到项目总线上， 利用cdn的形式引入各个框架的运行内核，有效提高缓存能力。使用订阅/发布的方式提供项目的总线订阅能力， 用以各个不同项目之间的通信。


## 搭建环境
这里推荐大家自己去搭建webpack 开发环境，`vue-cli`、 `create-react-app`虽好， 但很多时候并不能完全满足我们的需求。 而且你不明白它是怎么搭建起来的， 当有需要修改的时候就会束手无措。

当然本文的重点是微前端， 我重点贴出需要关键的配置代码


将 webpack 的 entry 改为你的组件, 然后加入 `webpack-system-register` 插件， 这个插件可以将webpack产出的代码变为 systemJs 所支持的模块格式.
> 在使用 `webpack-system-register`插件时请关闭`html-webpack-plugin`， 否则会报错


## 构建一个观察者
> 懒得上babel 转了， 所以直接写的es5的代码

大家可以网上早开源的库，我这里选择构建这样的一个 订阅/发布者模式， 就叫eventBus好了， 用于框架之间的通信， 这需要团队之间遵守规范

```js
(function(fn, global) {
  global.EventBus = fn()
})(function() {
  function EventBus() {
    this.observer = {};
  }

  // 监听事件
  EventBus.prototype.subscribe = function(eventName, callback) {
    const _this = this;
    // 判断该系列事件之前是否存在
    if (!_this.observer[eventName]) {
      _this.observer[eventName] = [];
    }
    // 添加到该事件名称的队列中
    _this.observer[eventName].push(callback);
    // 返回注销该事件的方法
    return function unsubscribe() {
      // 新的事件队列
      var newEventQueue = [];
      // 旧的事件队列
      var oldEventQueue = _this.observer[eventName];
      // 遍历事件队列
      for (var i = 0; i < oldEventQueue.length; i++) {
        // 获取当前回调事件
        const itemCallback = oldEventQueue[i];
        // 判断当前回调和订阅时的是否相同
        if (callback !== itemCallback) {
          // 不同则加入新的事件队列
          newEventQueue.push(itemCallback);
        }
      }
      // 覆盖之前的事件队列
      _this.observer[eventName] = newEventQueue;
    };
  };

  // 触发某一事件
  EventBus.prototype.dispatch = function(eventName) {
    const eventQueue = this.observer[eventName];
    // 确保该事件队列存在
    if (!eventQueue) {
      return false;
    }
    const args = Array.prototype.slice.call(arguments, 1)
    // 挨个调用订阅的回调
    for (var i = 0; i < eventQueue.length; i++) {
      const itemCallback = eventQueue[i];
      // 调用回调，传入参数， 进行this 的软绑定
      itemCallback.apply(null, args);
    }
  };
  return EventBus
}, window);
```

## 业务逻辑

分别用react 和 vue 实现一个点击按钮数字增加的小功能， 然后用上我们上面写的eventBus 这样的一个观察者，来进行订阅和发布， 打到框架和框架之间，项目和项目之间共享状态


### React 关键代码
```js
module.exports = {
  entry: './src/APP.jsx',
  // ....... 省略其他代码
  plugins: [
    new WebpackSystemRegister({}),
  ]
}
```
### Vue 项目关键代码
```js
module.exports = {
  entry: './src/App.vue',
  // ....... 省略其他代码
  plugins: [
    new WebpackSystemRegister({}),
  ]
}
```

### 构建vue项目逻辑
```js
<template>
  <div class="vue-wrap">
    vue 组件
    <p class="vue-wrap-status">vue组件内状态： {{vueCount}}</p>
    <div>
      <button @click="addVue">点我</button>
    </div>
    <hr />
    <p class="vue-react-status"> react组件内状态： {{reactCount}} </p>
    <div class="vue-react-trigger">
      <button @click="addReact">通信react </button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      vueCount: 0,
      reactCount: 0
    }
  },
  mounted() {
    window.eventBus.subscribe('addVueCount', (count) => {
      this.vueCount++
    })
  },
  methods: {
    addVue() {
      this.vueCount++ 
    },
    addReact() {
      this.reactCount++ 
      window.eventBus.dispatch('addReactCount', this.reactCount)
    }
  },
}
</script>
<style lang="scss">
.vue{
  &-wrap {
    border: 3px solid green;
    color: green;
    font-size: 22px;
    font-weight: bold;
    text-align: center;
  }
  &-react {
    &-trigger {
      padding-bottom: 20px;

    }
  }
}
</style>
```

此时的界面
![2019-09-17-22-40-23](http://img.nixiaolei.com/2019-09-17-22-40-23.png)


### 构建react项目逻辑

```js

class App extends Component {

  constructor(props) {
    super(props)
    // 订阅事件
    window.eventBus.subscribe('addReactCount', (count) => {
      this.setState({
        reactCount: count
      })
    })
    this.state = {
      reactCount: 0,
      vueCount: 0
    }
  }

  addReactHandle() {
    const { reactCount } = this.state
    this.setState({
      reactCount: reactCount + 1
    })
  }

  addVueHandle() {
    const { vueCount } = this.state
    const newCount = vueCount + 1
    // 派发事件
    window.eventBus.dispatch('addVueCount', newCount)
    this.setState({
      vueCount: newCount
    })
  }

  render() {
    return (
      <div className="react-wrap">
        <p>React 组件</p>
        <p>当前React 状态: { this.state.reactCount }</p>
        <button onClick={ () => this.addReactHandle() }>点我</button>
        <hr/>

        <p>当前Vue状态: { this.state.vueCount }</p>
        <button onClick={ () => this.addVueHandle() }>通信vue</button>
      </div>
    )
  }
}
```

此时的界面

![2019-09-17-22-37-15](http://img.nixiaolei.com/2019-09-17-22-37-15.png)

## 整合至总线

首先我们想让总线项目同时具有 vue, react 的开发环境， 我们首先需要将其依赖导入。
```js
// vue
<script src="https://cdn.staticfile.org/vue/2.6.10/vue.min.js"></script>
// react
<script src="https://cdn.staticfile.org/react/16.9.0-rc.0/umd/react.production.min.js" crossorigin></script>
<script src="https://cdn.staticfile.org/react-dom/16.9.0-rc.0/umd/react-dom.production.min.js" crossorigin></script>
```

对了， 不能漏了最关键的systemJS
```js
<script src="https://cdn.staticfile.org/systemjs/3.1.6/system.min.js"></script>
```

然后将我们之前写好的eventBus 引入
```js
<script src="./scripts/eventBus/eventBus.js"></script>
```

并将其绑定到 window 
```js
window.eventBus = new EventBus()
```

然后我们将vue 项目和 react项目分别打包出来， 放入总线项目中： 
这里的index.js 就是项目打包产出的文件，
需要注意的是,jsx想要在浏览器中运行， 我们还需要引入babel, 
```js
<script src="https://cdn.staticfile.org/babel-standalone/7.0.0-beta.3/babel.min.js"></script>
```
并且script标签上要加上`type="text/babel" data-type="react-app"` 


```js
<body>
  <div id="vue">
    <index></index>
  </div>
  <div id="react"></div>
</body>
<script>
new Vue({
  el: "#vue",
  components: {
    "index": () => System.import("./scripts/vue/index.js")
  }
})
</script>

<script type="text/babel" data-type="react-app">
  System.import("./scripts/react/index.js").then(_ => {
    const domContainer = document.querySelector('#react');
    const e = React.createElement;
    const App = _.default;
    ReactDOM.render(<App />, domContainer);
  })
</script>
```


让我们打开看一下此时的效果。
![微前端实现结果](http://img.nixiaolei.com/microferesult.gif)






## 结尾

一个基本的微前端架构成功实现了， 接下来我们需要做的更多的事情， 就是：


1. 解决整合的难度及成本， 可以编写shell脚本或者是构建一个额外的打包配置
2. 无法直观调试开发， 需要合并后， 才能看到， 无法热更新



## 项目代码地址
> https://github.com/nxl3477/note/tree/master/%E5%AE%9E%E6%88%98/%E5%BE%AE%E5%89%8D%E7%AB%AF%E6%9E%B6%E6%9E%84