---
title: 【React】革命性的fiber架构
date: 2019-04-15 23:03:00
categories: React
tags: React
---

[github地址](https://github.com/koba04/react-fiber-resources) 
↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑

## React Fiber异步渲染

### unstable_ConcurrentMode
`React.unstable_ConcurrentMode` 是一个使子组件中的更新异步的组件，这意味着更新被视为低优先级。
```Js
const ConcurrentMode = React.unstable_ConcurrentMode;
<ConcurrentMode>
  <App /> // Low Priority by default
</ConcurrentMode>
```
### flushSync
如果您想在组件内部使用同步更新，则可以使用ReactDOM.flushSync(cb)。在ReactDOM.flushSync回调内部，更新被视为同步优先级，这是v16的默认优先级。
```Js
flushSync(() => {
  this.setState({
    num: newNum,
  })
})
```









