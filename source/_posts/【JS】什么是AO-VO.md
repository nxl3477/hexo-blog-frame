---
title: 【JS】什么是AO-VO
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

AO是在函数执行的阶段
1. arguments的callee length
2. 内部定义的函数
3. 绑定对应环境变量
4. 内部定义的变量

AO的阶段又可以细分为:
1. 定义阶段
2. 执行阶段


#### 浏览器解析阶段
这就是浏览器解析js的时候， 当v8拿到函数的时候， 都给你先编译成比较慢的字节码， 然后对字节码进行优化， 接着经过`静态分析`进行本地替换那些频繁使用到的字节码段， 替换成优化后的字节码，为什么不都进行优化， 因为优化后的字节码体积会比较大。

#### 初始化阶段
创建作用域链（Scope Chain）
创建变量，函数和参数。
定义`this`但不确认





让我们通过这段代码看一下AO的具体情况:
```Js
function test(a, b) {
  var c = 10
  function d() {}
  var e = function _e(){};
  (function x() {})
}

test(10)
```
那此时这个 test 函数的AO情况如下

```Js
EC(test) = {
  // 自己维护的scopeChain 作用域链
  scopeChain: { Scope }, // 指向下面的Scope

  // 预解析的原理
  AO(test): {
    a: 10,
    b: undefind,
    c: undefind,
    e: undefind
  },
  this: '先定义但不确认',
  Scope: [AO, glbalContext.vo]
}


```

#### 执行阶段

```Js
EC(test) = {
  scopeChain: { Scope },

  // 预解析的原理
  AO(test): {
    arguments: {
      0: 11,
      1: 22,
      length: 3
    },
    a: 10,
    b: undefind,
    c: undefind,
    e: undefind
  },
  this: 'this指向函数执行栈栈顶',
  Scope: [AO, glbalContext.vo]
}

```





### VO
> Variable object 变量对象

每个函数都有自己的VO对象， 存储着这些
1. 函数声明（不包含函数表达式）
2. 函数的形参
3. 变量的声明


总的来说篇， VO要比AO的范围大得多， VO是负责把各个调用的函数串联起来的。 他是外部的， 而AO是函数自身内部的

距离， 当执行 `fun1`:
```js
VO(fun1) = AO // 执行到fun1 时可以在VO中的fun1就是此时的AO
```

又或者再举个例子，有这样一段代码要执行
```Js
var a = 10
function test(x) {
  var b = 20 
}

test(30)
```

此时我们来看一下VO的状态

```Js
// VO中的 GO 
VO(globalContext) = {
  a: 1,
  test: '<reference to function>'
};

// VO 中的 test函数

VO(test) = {
  x: 30, // 传入的参数
  b: 20
}
```











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

当一个函数入栈， 它所属的变量也随之入栈， 当函数出栈， 变量也就跟着出栈了， 每个函数都会拥有各自的 EC（函数执行上下文）， 也就有各自的 AO、VO, 

> 你肯定会疑惑闭包呢， 函数出栈了， 那闭包去哪了
> 其实，js的闭包是存在堆里的， 跟普通函数不一样， 所以不会跟着出栈函数一起被回收


