---
title: 【React】从高阶函数到理解高阶组件
date: 2019-04-09 22:13:49
categories: React
tags: React
---

高阶组件其实就是高阶函数

高阶函数的概念千万别和普通回调搞混， 高阶函数的必要条件：
* 一定要返回一个新函数
* 非入侵


## 一句话理解高阶函数
本来我没有的，我很垃圾，  通过把我交给别人， 在我身上装了额外的东西， 使我变的拥有了某种能力

举个例子:  人和坦克， 本来人是不能发射炮弹的， 但是人钻进了坦克， 就可以发射炮弹了， 人还是人（自身未变）， 只是在坦克里了


## 一个例子理解高阶函数

一个简单的不能再简单的高阶函数
```JavaScript
function hoc(fn) {
  return () => {
    console.log('start')
    fn()
    console.log('end')
  }
}
```

## 一个例子理解高阶组件
在不修改原有组件的情况下， 为其添加一个生命周期

```JavaScript
// 接收一个组件参数
const MyContainer = (WrappedComponent) => {
  return class extends Component {

    // 新增的生命周期
    componentwillmount() {
      console.log('增加一个生命周期')
    }

    render() {
      return (
        // 传入的组件
        <WrappedComponent />
      )
    }
  }
}
export default MyContainer;
```

























