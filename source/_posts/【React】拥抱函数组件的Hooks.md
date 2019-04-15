---
title: 【React】拥抱函数组件的Hooks
date: 2019-04-14 21:47:49
categories: React
tags: React
---

React16带来了惊人的改变， amazing~

## hooks带来的好处和特点

1. 只能在函数组件中使用
2. 函数组件后期的业务变更无需修改为class组件
3. 告别了麻烦`this` 和难记的生命周期
4. 合并了生命周期`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的作用
5. 可以包装自己的`hooks`，基于纯命令式的`API`
6. 更好的完成状态之间的共享， 解决原来class组件内部封装问题。 也解决高阶组件嵌套过深
7. `useReducer`集成`redux`
8. `useEffect`接受脏操作等到`react`更新了`dom`后， 它再依次执行我们定义的副作用函数。 这里就是一个`io`且是异步的


## 起步
先来看看最常用的两个hooks： `useState`, `useEffect` 

### useState
> 传入一个初始值， 返回 `count`、`setCount`,  
> `count`用来读， `setCount`用来写  


```Js
export default () => {
  const [ count, setCount ] = useState(0)
  return (
    <div>
      count: { count }
      {/* 点击事件触发setCount方法 */}
      <button onclick={ setCount }>增加Count</button>
    </div>
  )
}
```
此时页面就是这个样子的
![当前页面状态](http://img.nixiaolei.com/2019-04-14-22-35-19.png)

然后我点击一次按钮
![点击后](http://img.nixiaolei.com/2019-04-14-22-43-40.png)

此时`count`就发生了变化变为了`1`， 就好像我们使用了`setState`方法一样神奇


### useEffect
> 副作用， 如果你对函数式编程有一定的了解， 那应该能明白这个词

它主要用来承担`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的作用

它传入一个函数，用于在组件`render`时触发， 其中需要返回一个函数，返回的函数在组件卸载时被触发
```Js
useEffect(() => {
  console.log("更新渲染阶段")
  document.title = `标题-${count} times`
  return () => {
    console.log("卸载阶段")
  }
})
```

在`useState`例子基础上我们添加了`useEffect`后， 效果如下：

![useEffect](http://img.nixiaolei.com/effect.gif)

我们可以看到， 刷新页面后首先出现的是"更新渲染阶段"，
当我们点击了按钮后， 立即出现了“卸载阶段”， 然后出现了"更新渲染阶段"

这就证明该副作用是在每次组件更新时都会被触发的。


#### 自定义触发
`useEffect`不仅能够替代那些个老生命周期， 还能够自定义根据某个值来触发

`useEffect`的能接收两个参数， 第一个就是刚刚我们尝试的回调， 第二个参数可传可不传， 他是一个数组， 

如果你不传，就和上面的例子一样， 默认全部触发，
如果你传入的是一个空数组`[]`，那就只在第一次渲染时触发
如果你数组里制定了值， 那就只在这些值变化时触发

***

比如我要指定只有 `count`变化时才会触发
```Js
useEffect(() => {
  console.log("更新渲染阶段")
  document.title = `标题-${count} times`
  return () => {
    console.log("卸载阶段")
  }
}, [count])
```

| api | ability | 
| :------: | :------: | 
| state | 返回有状态值, 以及更新这个状态值的函数 | 
| useEffect | 接收包含命令式， 可能有副作用代码的函数 |
| useContext | 接受上下文对象（从React.createContext返回的值） |
| useReducer  | useState的替代方案 |
| useCallback | 返回一个回忆的memoized版本， |
| useMemo | 纯的一个记忆函数 |
| useRef | 返回一个可变的ref对象 |
| useImperativeMethods  | 自定义使用ref时公开给父组件的实例值 |
| useMutationEffect  | 更新兄弟组件之前， 它dom改变前执行 |
| useLayoutEffect  | Dom改变后同步触发 |
