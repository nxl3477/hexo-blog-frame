---
title: 【React】PureComponent解决了什么问题
date: 2019-04-04 15:10:16
categories: React
tags: React
---

`PureComponent` 是和 `shouldComponentUpdate`这个生命周期息息相关的

## React 重新渲染问题

`React`中，当父组件中触发`setState`， 尽管未修改任何 `state` 中的值也会引起所有子组件的重新渲染， 更何况是修改了某个`state`

还有， 当父组件传给子组件的`props` 发生改变， 不管该`props`是否被子组件用到， 都会去重新渲染子组件。


> 其实我们也可以想得到， setState 会去触发 render， 所以父组件`render`函数中的子组件都会被重新渲染， 因此也就无关 `state` 与 `props`了


针对这个问题我实现了个例子， 来看一下下面这个例子

### 实现下问题
> setState但未修改任何state

父组件触发 `setState` 方法, 但未更新任何`state`

```JsX
import TodoItem from './components/todoItem/todoItem'
// 父组件代码
class TodoList extends Component {
  noChange() {
    console.log('触发setState')
    this.setState(() => ({
    }))
  }
  render() {
    const { todoList } = this.state
    return (
      <div className="wrap">
        <button onClick={() => this.noChange()}>没有变化的setState</button>
        {
          todoList.map( (item,index) => <TodoItem key={index}  data={item} /> )
        }
      </div>
    )
  }
}

// 子组件代码
class TodoItem extends Component {
  constructor(props) {
    super(props)
  }

  componentWillUpdate() {
    console.log('我被更新了')
  }

  render() {
    return (
      <div>
        { this.props.data.thing }
      </div>
    )
  }
}
```



我们在子组件中预留了`componentWillUpdate` 方法， 用来监测子组件是否被跟新

```JavaScript
componentWillUpdate() {
  console.log('我被更新了')
}
```

实验结果: 

![noChangeState](http://img.nixiaolei.com/noChangeState.gif)



我们可以看到, 每一次的点击都引起了子组件的`update`


### 冷静分析问题

无故的重复`update`, 这会导致业务规模扩大后十分的影响性能.

为此我监测了一下页面的重绘事件:
> 绿色区域是浏览器发成重绘的地方

![noRePaint](http://img.nixiaolei.com/noRePaint.gif)

可以看到`todoList`其实并没有引起浏览器的`repaint` , 因此可以推测, 实际`dom`并没有更新这在渲染页面前被`dom diff`给排除掉了, 因此性能损耗在了转换成`Virtual DOM`的过程中 



## shouldComponentUpdate
> shouldComponentUpdate(nextProps, nextState)， 默认返回true

`shouldComponentUpdate` 是 `React` 中做性能优化的重要手段， 看这个英文翻译我们大概也能猜出个一二来 --- '组件是否跟新？'

`React` 会根据 `shouldComponentUpdate` 的返回结果来决定该组件是否重新渲染， 如果返回`True`就渲染，  如果返回`False`就重新渲染

基于这个特性, 我们来修改一下上面问题中的代码

```JavaScript
// 父组件无变化, 因此省略

// 子组件代码
class TodoItem extends Component {

  // ...省略其他原有的代码


  // --------- 新增代码 -------------
  shouldComponentUpdate(nextProps, nextState) {
    // 判断下 当前 props传入的data.thing  是否和新传入的  data.thing 相同
    return  this.props.data.thing !== nextProps.data.thing
  }
  // --------- 新增代码 -------------
}
```

实验结果:
![addShouUpdate](http://img.nixiaolei.com/addShouUpdate.gif)

我们可以看到`update`事件确实消失了



## PureComponent
看了`shouldComponentUpdate`函数的作用, 就能够理解`PureComponent`了?  它其实就是在帮我们做这样一件事:

**自动的帮我们编写 `shouldComponentUpdate` 方法, 避免我们为每个组件都编写一次的麻烦**

我们只需要这样, 就可以一步到位
```JavaScript
import React, { PureComponent } from 'react'
class TodoItem extends PureComponent {
}
```


***

是不是感觉`PureComponent`很强大, 但`PureComponent`并不完美, 

其实它还有一些潜藏的问题, 如果您想要继续了解, 请在站内搜索 `immutable`





 




















