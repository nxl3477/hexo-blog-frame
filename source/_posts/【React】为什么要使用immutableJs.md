---
title: 【React】为什么要使用immutableJs
date: 2019-04-08 22:10:17
categories: React
tags: [React, 性能优化]
---


## React自身的一些Bug
在开发中对`React`应用做性能优化时， 我们通常会使用`shouldComponentUpdate` 这个生命周期钩子来决定该组件是否需要更新， 来过滤掉没有意义的更新修改。 

并且，官方也贴心的为我们提供了 `PureComponent` 用来省去我们繁琐的判断， 自动生成`shouldComponentUpdate`



但现实往往不是那么的完美， `PureComponent` 也有解决不了问题的时候， 

为了更好的理解， 我们先来显示的使用`shouldComponentUpdate`操作一遍

```JavaScript
// 父组件
class TodoList extends Component {
  constructor(props) {
    super(props)
    this.state = { 
      todoList: [
       '喝可乐', 
       '打游戏'
      ] 
    }
  }

  shouldComponentUpdate(nextProps, nextState) {
    if( this.state.todoList != nextState.todoList ) {
      console.log('两次不一致， 同意更新')
      return true
    }
    console.log('更新前后todolist 一致， 所以不更新')
    return false
  }

  // 数组concat操作， 会返回新数组
  concatHandle() {
    let todoList = this.state.todoList
    const rd = Math.random() * 30
    todoList = todoList.concat([ `搞事情:${rd}` ])
    this.setState(() => ({
      todoList
    }))
  }

  // 数组Push操作， 未改变原数组
  pushHandle() {
    let todoList = this.state.todoList
    const rd = Math.random() * 30
    todoList.push( `搞事情:${rd}` )
    this.setState(() => ({
      todoList
    }))
  }

  render() {
    const { todoList } = this.state

    return (
      <div className="wrap">
        <button onClick={() => this.pushHandle()}>Push操作</button>
        <button onClick={() => this.concatHandle()}>concat操作</button>
        {
          todoList.map( (item,index) => <TodoItem key={index}  thing={item} /> )
        }
      </div>
    )
  }
}

// 子组件
class TodoItem extends Component {
  constructor(props) {
    super(props)
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

我们在`shouldComponentUpdate` 进行了简单的判断， 但是可悲的是这只在对比普通类型时比较的奏效， 因为`JavaScript`中存在这引用类型的概念， 所以对于引用类型来说`==` 会变成判断该变量地址的操作

口说无凭，我们来看看实际的情况。 

![ReactBug](http://img.nixiaolei.com/pureCompoents.gif)


我的天，这是怎么回事， 实际情况好像要更复杂一些！
点击了两次`Push`没有反应， 然而在点击`concat`的时候， 一瞬间多出了`3`条


### 冷静分析
对于这种种非正常现象， 我们有必要保持清新的头脑去分析一下

![别慌](http://img.nixiaolei.com/2019-04-08-22-39-14.png)

我们可以看到， 第一次点击`push`操作， 页面没有任何反应， 只是在控制台打印了`更新前后todolist 一致， 所以不更新`,

哦~， 看到这个我们知道了， 此次组件的更新被`shouldComponentUpdate`拦截了，  这怎么行呢， 我要更新的你给我拦截了， 我数组明明变化了啊， 我数组新增了内容， 你就该给我正常变化， 这不是坑人吗


### PureComponent
看到这，你可能会说， 官方不是提供了`PureComponent`吗，  官方给的还能跟你一样这么龊？  该更新的不更新？

您还别不信， 它就这么龊， `PureComponent` 其实是很傻的， 他也只是单纯的帮我们做了上面我们那样的操作， 虽然这样针对基本类型来说已经非常够用了， 能够解决问题了， 但是一旦遇上了引用类型， 它可就歇菜了， 还会引起你页面的奇妙`Bug`

不信咱们来测试下， 我们修改下父组件代码， 子组件保持一致: 
```JavaScript
// 替换为 PureComponent
class TodoList extends PureComponent {
  constructor(props) {
    super(props)
    this.state = { 
      todoList: [
       '喝可乐', 
       '打游戏'
      ] 
    }
  }

  //  ------------ 删除掉 shouldComponentUpdate ------------------------
  // shouldComponentUpdate(nextProps, nextState) {
  //   if( this.state.todoList != nextState.todoList ) {
  //     console.log('两次不一致， 同意更新')
  //     return true
  //   }
  //   console.log('更新前后todolist 一致， 所以不更新')
  //   return false
  // }

  concatHandle() {
    let todoList = this.state.todoList
    const rd = Math.random() * 30
    todoList = todoList.concat([ `concat操作: ${rd}` ])
    this.setState(() => ({
      todoList
    }))
  }

  pushHandle() {
    let todoList = this.state.todoList
    const rd = Math.random() * 30
    todoList.push( `push操作:${rd}` )
    this.setState(() => ({
      todoList
    }))
  }

  render() {
    const { todoList } = this.state

    return (
      <div className="wrap">
        <button onClick={() => this.pushHandle()}>Push操作</button>
        <button onClick={() => this.concatHandle()}>concat操作</button>
        {
          todoList.map( (item,index) => <TodoItem key={index}  thing={item} /> )
        }
      </div>
    )
  }
}
```

来看看结果

![测试结果](http://img.nixiaolei.com/pureComponent02.gif)


哎， 还是一样的坑

那这个`Bug`怎么办， 这时候就请本文的主角`immutableJs`登场吧


## immutableJs
> Facebook 工程师 Lee Byron 花费 3 年时间打造，与 React 同期出现，但没有被默认放到 React 工具集里（React 提供了简化的 Helper）。它内部实现了一套完整的 Persistent Data Structure，还有很多易用的数据类型。像Collection、List、Map、Set、Record、Seq。有非常全面的map、filter、groupBy、reduce、find函数式操作方法。同时 API 也尽量与 Object 或 Array 类似。


使用 `immutableJs` 避免这个副作用的一种实现是按值传递，也就是拷贝一份再传递过去，有深层结构就深拷贝。深拷贝在只做局部修改的时候做了很多无用功，于是`ImmutableJs`做了性能优化。


### 使用它带来的好处

网上找了个图，假如我们要修改左图中黄色节点的子节点4，那么Immutable.js只需要更新右图中的绿色节点，其余节点不需拷贝，继续复用。也就是说，Immutable.js会更新从根节点到所修改节点路径上的所有节点，由于修改了根节点，所以返回一个新对象，这也解释了为什么能控制副作用。

![更新树](http://img.nixiaolei.com/2019-04-09-21-43-20.png)


1. 假如你在组件state中保存了一份有深层结构的引用类型的数据，如果没有Immutable.js，你需要深拷贝一份再做修改。而用Immutable.js将state中的数据包装一下，不需深拷贝就可以直接修改。
2. 由于修改后返回的是新对象，React.js只需要在oldState.obj === newState.obj这一层就能判断出obj产生了变化，不需要深入obj的深层结构。
3. 带来几种操作方便的数据结构和API


