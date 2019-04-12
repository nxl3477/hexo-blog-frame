---
title: 【React】react16新增错误处理生命周期
date: 2019-04-12 23:49:46
categories: React
tags: [React, 错误处理]
---

`React16`新增了一个用于错误处理的生命周期，

有了这个法宝，我们就能针对组件的错误做很多事情啦， 来看看怎么用

## 使用示例
```Js
export default class Errorfun extends Component {
  constructor( props) {
    super(props)
    this.state = { hasError: false }
  }
  // 捕捉错误和错误上报程序库一起使用
  componentDidCatch(err, info) {
    this.setState({ hasError: true })
  }
  render() {
    if ( this.state.hasError ) {
      return <div>Somthing error </div> 
    }
    return (
      <div>
        Hello React
      </div>
    )
  }
}
```












