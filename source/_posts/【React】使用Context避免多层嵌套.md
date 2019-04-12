---
title: 【React】使用Context避免多层嵌套
date: 2019-04-12 22:39:19
categories: React
tags: React
---

用过redux + react-redux的同学，应该会觉得新的Context API很眼熟。而有看过react-redux源码的同学就知道，react-redux本身就是基于旧版本的Context API实现的。

既然已经有了现成的解决方案，为什么还会有新的Context API呢？

1. 现有Context API的实现存在一定问题：比如当父组件的shouldComponentUpdate性能优化，可能会导致消费了context数据的子组件不更新。
2. 降低复杂度：类似redux全家桶这样的解决方案，给项目引入了一定的复杂度，尤其是对方案了解不足的同学，遇到问题可能一筹莫展。新Context API的引入，一定程度上可以减少不少项目对redux全家桶的依赖。
3. 解决多层组件嵌套


## 创建一个上下文

利用新的`API`创建一个上下文， 接收返回的`Provider`, `Consumer`
```Js
import React, { Component } from 'react'
const { Provider, Consumer } = React.createContext("default")
```

## 定义父容器

在此父容器内部使用`Provider`包裹住插槽传入的子组件， 传入`Provider`的值和`state`挂钩， 并且我将两个值都实现了双向绑定，以便于查看使用`context`方法传递的变量能否及时刷新  

```Js
class Parent extends Component {
  state = {
    name: "张三",
    age: 12
  }

  render() {
    const { name, age } = this.state
    return (
      <div>
        姓名：
        <input  onChange={ e=> this.setState({ name: e.target.value })} value={name} />
        年龄:
        <input  onChange={ e=> this.setState({ age: e.target.value })}  value={age} />

        <Provider value={{ name, age }}>
          {this.props.children}
        </Provider>
      </div>
    )
  }
}
```

## 创建两个子组件
两个子组件分别用接收一个参数， 当然你也可以接收多个的
```Js
// 我用来展示name
function Child01() {
  return (
    <Consumer>
      {
        value => (
          <div>{value.name}</div>
        )
      }
    </Consumer>
  )
}
// 我用来展示age
function Child02() {
  return (
    <Consumer>
      {
        value => (
          <div>{value.age}</div>
        )
      }
    </Consumer>
  )
}
```

## 组件整合
用`Parent`组件将两个`Child`包裹， 也就是上面说的插槽
```Js
export default () => (
  <Parent >
    <Child01 />
    <Child02 />
  </Parent>
)
```

最终效果:

![最终效果](http://img.nixiaolei.com/reactContext.gif)


## 完整代码
```Js
import React, { Component } from 'react'
const { Provider, Consumer } = React.createContext("default")
class Parent extends Component {
  state = {
    name: "张三",
    age: 12
  }

  render() {
    const { name, age } = this.state
    return (
      <div>
        姓名：
        <input  onChange={ e=> this.setState({ name: e.target.value })} value={name} />
        年龄:
        <input  onChange={ e=> this.setState({ age: e.target.value })}  value={age} />

        <Provider value={{ name, age }}>
          {this.props.children}
        </Provider>
      </div>
    )
  }
}

function Child01() {
  return (
    <Consumer>
      {
        value => (
          <div>{value.name}</div>
        )
      }
    </Consumer>
  )
}

function Child02() {
  return (
    <Consumer>
      {
        value => (
          <div>{value.age}</div>
        )
      }
    </Consumer>
  )
}

export default () => (
  <Parent >
    <Child01 />
    <Child02 />
  </Parent>
)
```







