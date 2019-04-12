---
title: 【React】高阶用法
date: 2019-04-02 10:55:50
categories: React
tags: React
---



## 默认参数
> 当未传入props时使用预设的默认值

使用静态属性`defaultProps`来定义`props`的默认值, 避免未传`props`报错的情况
```JavaScript
static defaultProps = {
  thing: "这是默认的props参数"
}
```

### demo
```JavaScript
// todoList.jsx
class TodoList extends Component {
  constructor(props) {
    super(props)
    this.state = { 
      todoList: [
        { thing: '喝可乐' },
        { thing: '打游戏' },
        // 故意留空一个
        {  },
        { thing: '看电影' },
        { thing: '看剧' },
        { thing: '刷抖音' },
        { thing: '吃饭' }
      ] 
    }
  }

  render() {
    const { todoList } = this.state
    return (
      <div className="wrap">
        {
          todoList.map( item => <TodoItem  thing={item.thing} />)
        }
      </div>
    )
  }
}

// todoItem.jsx
class TodoItem extends Component {
  constructor(props) {
    super(props)
  }
  static defaultProps = {
    thing: "这是默认的props参数"
  }
  render() {
    return (
      <div>
        { this.props.thing }
      </div>
    )
  }
}
```

实际显示:
![props默认值](http://img.nixiaolei.com/2019-04-07-11-28-59.png)



## 自由的render返回值类型
> `render`函数的要求没有以前严格了， 不用固定返回`dom` 以及 免除必须使用`()`包裹

### 返回dom
```JavaScript
class Demo extends Component {
  render() {
    return <div>Hello React</div>
  }
}
```

### 返回字符串
```JavaScript
class Demo extends Component {
  render() {
    return 'Hello React'
  }
}
```

### 返回数组
```JavaScript
class Demo extends Component {
  render() {
    return [
      <li>1111</li>,
      <li>2222</li>,
      <li>3333</li>,
    ]
  }
}
```



## 空的根节点
> 避免了无故地嵌套

写法一： 
```JavaScript
class Demo extends Component {
  render() {
    return <>
      <li>hello world1</li>
      <li>hello world2</li>
      <li>hello world3</li>
    </>
  }
}
```

写法二 ：
```JavaScript
// 使用文档片段
import React, { Component, Fragment } from 'react'

class Demo extends Component {
  render() {
    return <Fragment>
      <li>hello world1</li>
      <li>hello world2</li>
      <li>hello world3</li>
    </Fragment>
  }
}
```

两种写法的效果是一样的：
![空地根节点](http://img.nixiaolei.com/2019-04-07-12-57-12.png)

## PureComponent
> 自动为状态组件添加`shouldComponentUpdate`函数

```JavaScript
import React, { PureComponent } from 'react'
class TodoItem extends PureComponent {
  
}
```

## 更安全的数据类型
使用`immutableJs`


## 处理异步任务及组件
站内搜索 `react16.6中如何处理异步`

## 为函数组件做性能优化
站内搜索 `memo`


## 新版ref用法
站内搜索 `react16中如何使用ref`




