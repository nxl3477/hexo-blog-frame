---
title: 【React】react16.6中如何处理异步&&按需加载
date: 2019-04-11 21:48:06
categories: React
tags: [React, 性能优化]
---

当前大部分 `React` 应用需要使用 `code splitting` 的时候，都选择使用优秀的 `react-loadable` 来处理检测代码段是否已加载。然而，随着`React v16.6` 的发布，我们有一个非常难得的机会 ，可以删除我们的第三方依赖！

[React.Suspense](https://reactjs.org/docs/code-splitting.html)是一个新添加到核心React库中的功能，他的功能基本和 `react-loadable` 一致，所以不用多说，让我们来看看用 `React.Suspense` 替换 `react-loadable`。

`Suspense` 的好处是不一定只能处理组件的按需加载， 也能用来处理其他的异步事件

## 处理异步
> 自己实现流程

首先创建一个`Promise` ， 模拟一个异步请求
```Js
function fetchApi() {
  const promise = new Promise(resolve => {
    setTimeout(() => {
      resolve('data resolved')
    }, 3000)
  })
  return promise
}
```

实现处理`promise`异步的逻辑
```Js
import React, { Suspense, Component } from 'react'
// 创建Fetcher 
var cached = {}
// 接收一个为promise 的参数
const createFetcher = promiseTask => {
  // 将ref 复制为 cached
  let ref = cached
  return () => {
    // 返回一个 promise
    const task = promiseTask()
    // task
    task.then(res => {
      // ref 复制为 res, 也就是promise 的返回结果
      ref = res
    })
    //  ------注意此处的 console --------
    console.log('进入ref === cached的判断')
    // 如果 ref 没有发生改变， 仍旧和cached相等
    if (ref === cached) {
      // 抛出 task 
      throw task
    }
    // 得到结果输出
    console.log('🍎', ref)
    // 正常返回 ref ， 此时已是promise 的结果
    return ref
  }
}
```


我们将异步事件传入刚刚定义的`createFtecher`
```Js
const requestData = createFetcher(fetchApi)
```


创建一个函数组件， 用于显示处理好的异步结果
```Js
function SuspenseComp() {
  const data = requestData()
  return <p className="name">{data}</p>
}
```

重点来了， 使用`Suspense`组件包裹住， 使用`fallback`参数传入未加载完成的时候的样式, 我们这里传入一个`loading`的文字意思意思
```Js
class Test extends Component {
  render() {
    return (
      <Suspense fallback={<div>loading</div>} >
        <SuspenseComp />
      </Suspense>
    )
  }
}
```

来看一下效果， 在经过一段事件的Loading 后成功的显示除了我们在`setTimeout`中返回的文字

![实现效果](http://img.nixiaolei.com/suspense01.gif)


### 原理分析
仔细观看上面的效果图

我们看到`进入ref === cached的判断` 这条语句出现了二次， 继而才出现了最终结果`data resolved`，
还记得在`createFetcher`函数中抛出的`task`吗， 它就是利用这样一种方法， 抛出错误，让`Suspense` 来接收， 如果抛出了错误， 就隔一会继续来一次， 

其实就可以理解为轮询


## 处理组件
组件就比上面简单多了, 

`React`就提供了一个 `lazy`方法

我们只需要这样即可， 非常的快捷
```Js
lazy(() => import("./xxx"))
```

来看看实际如何使用
```Js
// 引入`lazy` 和 `Suspense`
import React, { Suspense, Component, lazy } from 'react'


// 加载异步组件
const LazyComp = lazy(() => import("./lazy"))

class Test extends Component {
  render() {
    return (
      <div>
        <Suspense fallback={<div>loading</div>} >
          { /* lazy 组件 */ }
          <LazyComp /> 
        </Suspense>
      </div>
    )
  }
}

// 被加载的lazy组件
class Lazy extends Component {
  render() {
    return (
      <div>
        my name lazy
      </div>
    )
  }
}
```

来， 让我们看一下效果

![实现效果](http://img.nixiaolei.com/lasy01.gif)



## 加载顺序
那上面两个例子中的组件`SuspenseComp` 和  `LazyComp` 都放在`Suspense` 里面呢，到底是各管各的还是一起完成呢？ 让我们实践一下
```Js
class Test extends Component {
  render() {
    return (
      <Suspense fallback={<div>loading</div>} >
        <SuspenseComp />
        <LazyComp />
      </Suspense>
    )
  }
}
```

效果如下:
![实现效果](http://img.nixiaolei.com/lazy02.gif)


我们可以看到,尽管`SuspenseComp` 和`LazyComp` 加载速度不同, 但是它仍旧是等待`Suspense` 中的所有组件都加载完成后才显示出来

## 处理异步-Hooks版本

Hooks 处理异步只需要短短的几行，就有了相同的效果
```Js
import { useFetch } from 'react-hooks-fetch'

function SuspenseComp() {
  const {error, data} = useFetch("a.php")
  if( error ) return <span>出错了🙂</span>
  if( !data ) return null
  return <span>result{data.title}</span>
}
```

真香




## 非Hooks版本全部代码
```Js
import React, { Suspense, Component, lazy } from 'react'

// React 自带的lazy组件，自己会编译 （避免被webpack打包成js）
const LazyComp = lazy(() => import("./lazy"))

function fetchApi() {
  const promise = new Promise(resolve => {
    setTimeout(() => {
      resolve('data resolved')
    }, 3000)
  })
  return promise
}

// 创建Fetcher 
var cached = {}
const createFetcher = promiseTask => {
  let ref = cached
  return () => {
    // 返回一个promise
    const task = promiseTask()
    task.then(res => {
      ref = res
    })
    console.log('进入ref === cached的判断')
    if (ref === cached) {
      throw task
    }
    // 得到结果输出
    console.log('🍎', ref)
    return ref
  }
}

const requestData = createFetcher(fetchApi)
function SuspenseComp() {
  const data = requestData()
  return <p className="name">{data}</p>
}

class Test extends Component {

  render() {
    return (
      <div>
        <Suspense fallback={<div>loading</div>} >
          <SuspenseComp />
          <LazyComp />
        </Suspense>
      </div>
    )
  }
}
export default Test
```






















