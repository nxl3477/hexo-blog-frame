---
title: '【React】react16中如何使用ref '
date: 2019-04-12 23:27:49
categories: React
tags: React
---

在 React16 新版本中，新引入了 React.createRef 与 React.forwardRef 两个 API，有计划移除老的 string ref，使 ref 的使用更加便捷与明确。如果你的应用已经升级到 React16.3+ 版本，那就放心大胆使用 React.createRef 吧，如果暂时没有的话，建议使用 callback ref 来代替 string ref。

> 新版的`ref`内部使用`Symbol`来作为标识， 可以猜测出，`React`的用意是想避免`string ref`可能引起重复的问题

## 关联ref

使用新版本的ref
```Js
export default class Reftest extends Component {
  constructor(props) {
    super(props);
    // 创建ref
    this.myRef = React.createRef();
  }
  componentDidMount() {
    // 打印ref
    console.log(this.myRef)
    this.myRef.current.focus();
  }
  render() {
    // 挂载 ref
    return <input ref={this.myRef} />;
  }
}

```


## 传递和转发ref


利用 `React.forwardRef` 传递复用 `ref`

```Js
import React, { Component } from 'react'

const TargetComponent = React.forwardRef((props, ref) => {
  return <input type="text" ref={ref} />
})

export default class Reftest extends Component {
  constructor(props) {
    super(props);
    // 创建ref
    this.myRef = React.createRef();
  }
  componentDidMount() {
    // 改变value
    this.myRef.current.value = "传递ref成功"
  }
  render() {
    // 挂载 ref, 让组件也保持ref写法
    return <TargetComponent ref={this.myRef} />
  }
}
```

结果如下: 

![ref复用](http://img.nixiaolei.com/2019-04-12-23-44-48.png)

参考文献:
* https://blog.csdn.net/qq_24147051/article/details/81218688












