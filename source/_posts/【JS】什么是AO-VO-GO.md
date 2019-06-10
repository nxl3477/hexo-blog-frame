---
title: 【JS】什么是GO-EC-VO-AO
date: 2019-06-10 21:34:38
categories: JavaScript
tags: [JavaScript, js执行机制]
---



## GO
> glbal Context 全局执行上下文

首先在js执行的时候会创建一个GO， 被压入 ESC Stack 栈底


## EC
> execution 执行上下文

在每个函数执行的时候会创建一个EC（执行上下文）

在函数执行上下文中， 还会有两个东西被创建出来： AO、VO

### AO
> Activation Object 活动对象

### VO
> Variable object 变量对象



***
上面的这些也就构成了 ECS Stack 函数执行栈

### ESC Stack
> executions statck  执行上下文栈

很多个函数存在一起的时候会存在一个ESC Stack

所谓的 stack 它就是一个栈， 先进入

当js开始运行时就创建一个 `globalContext` 压入栈底， 也就是单体内置对象`Global`, 在浏览器中通常是 `window`

被压入栈的情况大致如下
```Js
ECStatck = [
  globalContext
]
```

当一段代码执行了好多函数时， 比如这样一段代码: 
```js
function fun3() {}

function fun2() {
  fun3()
}

function fun1() {
  fun2()
}
fun1()
```

实际入栈过程是这样的:
```Js
ECStatck = [
  fun3,
  fun2,
  fun1,
  globalContext
]
```

当一个函数入栈， 它所属的变量也随之入栈， 当函数出栈， 变量也就跟着出栈了， 

你肯定会疑惑闭包呢， 函数出栈了， 那闭包去哪了

其实，js的闭包是存在堆里的， 跟普通函数不一样， 所以不会跟着出栈函数一起被回收