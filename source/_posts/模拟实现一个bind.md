---
title: 模拟实现一个bind
date: 2019-03-31 16:52:35
categories: JavaScript
tags: JavaScript
---

`bind`方法用来保留`JavaScript`的`this`指向， 和`apply`、`call`这两个方法不同的是，`bind`不会立即执行

如何模拟实现一个`bind`方法

初始函数，我们将对整个函数修改`this`指向
```JavaScript
function fn( name ) {
  this.name = name
}

var obj = { age: 18 }
```
## 最普通的实现
创建一个函数
```JavaScript
function mybind(func ,context, ...args) {
  return () => {
    return func.apply(context, args  )
  }
}

var bindFn = mybind(fn, obj, '小明')
```

## 原型实现
创建一个函数
```JavaScript
Function.prototype.mybind = function(context, ...args) {
  const _this = this
  return  (...args2) => {
    return _this.apply( context, [ ...args, ...args2] )
  } 
}

var bindFn = fn.mybind(obj, '小明' )
```



实现很简单， 但是还差一步，就是`bind`后的函数， 拿来作为构造函数`new`时， `this`指向会出问题


## 处理new 的指向

```JavaScript
Function.prototype.mybind = function(context, ...args) {
  const _this = this
  function cbFn (...tempArgs) {
    return this instanceof cbFn ? _this.apply(this, [ ...args, ...tempArgs]) : _this.apply( context, [ ...args, ...tempArgs] )
  } 
  return cbFn
}
var BindFn = fn.mybind(obj, '小明' )
var bfn = new BindFn()
```


参考文献
* https://github.com/mqyqingfeng/Blog/issues/13



















