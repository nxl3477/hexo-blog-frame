---
title: 【React】memo为函数组件创造shouldComponentUpdate
date: 2019-04-12 22:09:22
categories: React 
tags: [React, 性能优化]
---


`React v16.6.0`出了一些新的包装函数(`wrapped functions`)，一种用于函数组件`PureComponent / shouldComponentUpdate`形式的`React.memo()`

> React.memo()是一个高阶函数，它与 React.PureComponent类似，但是一个函数组件而非一个类。

利用`memo`我们就能函数组件创造为`Purecomponent`

总之就是， 在今天，我们也能为函数组件做性能优化了， 就让我们来演示下具体如何使用

## 先创建一个普通的函数组件

这是一个最普通的函数组件， 接收一个`name`的`props`参数
```Js
// 创建一个函数组件
function Child({ name }) {
  console.log('I am rendering')
  return <div>Memo组件 name => { name }</div>
}
```
## 显式地shouldComponentUpdate
然后我们为其量身定制一个`shouldComponentUpdate`
```Js
function areEqual(prevProps, nextProps) {
  if( prevProps.name == nextProps.name ) {
    return true
  } else {
    return false
  }
}
```
## 产生出一个新组件
利用`memo`这个高阶函数，我们创造出一个新的组件，这将会是个拥有`shouldComponentUpdate`的函数组件
```Js
import React, { memo, Component } from 'react'
const DemoComponent = memo(Child, areEqual)
```

## 查看结果
最后我们将其放入`render`函数， 让它渲染出来， 并传入刚刚设定的`name`参数
```Js

export default class Menotest extends Component {
  render() {
    return (
      <div>
        {/* seconds作为Props传入 */}
        <DemoComponent name="小明" />
      </div>
    )
  }
}
```


结果如下:
![2019-04-12-22-30-31](http://img.nixiaolei.com/2019-04-12-22-30-31.png)

*** 

## 完整代码
```Js
import React, { memo, Component } from 'react'

// 创建一个函数组件
function Child({ name }) {
  console.log('I am rendering')
  return <div>Memo组件 name => { name }</div>
}
// 注意 函数组件没有state , 显式的shouldComponentUpdate
function areEqual(prevProps, nextProps) {
  if( prevProps.name == nextProps.name ) {
    return true
  } else {
    return false
  }
}
const DemoComponent = memo(Child, areEqual)

export default class Menotest extends Component {
  render() {
    return (
      <div>
        {/* seconds作为Props传入 */}
        <DemoComponent name="小明" />
      </div>
    )
  }
}
```







