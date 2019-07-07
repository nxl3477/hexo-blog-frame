---
title: 【Redux】react、redux与函数式编程
date: 2019-07-07 18:44:26
categories: React
tags: [React, Redux]
---


> redux 本身是container ， state就是value ， action 就是 变形关系

1. 可以将React看作输入为state， 输出为view的“纯”函数
2. 范畴论将世界抽象为对象和对象之间的联系， Redux将所有事件抽象为 `action` (变形关系)
3. container 有_value  和 map 两个属性， 而修改 _value 的方法直有 map  , 在操作完 _value 后将新值放回 Container中
4. 如何操作或修改_value 由f给出
5. store 是一个容器含有state 和 reducer , 这从store 的创建语句， 并且currentState在修改完后将新值依然存放在 store内。
如何修改 currentState 是根据用户操作 action


```js
store -> container
currentState -> _value
action -> f
currentReducer -> map
middleware -> IO functior // 解决异步操作的各种问题
```













