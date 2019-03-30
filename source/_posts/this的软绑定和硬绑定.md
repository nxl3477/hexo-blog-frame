---
title: this的软绑定和硬绑定
date: 2019-03-30 14:11:56
categories: JavaScript
tags: JavaScript
---

被调用的函数示例
```JavaScript
function fn() {
  // 如果严格模式， "use strict"加在此处
  console.log(this)
}
```

## 软绑定
```JavaScript
fn() // window
```
"use strict" 严格模式的软绑定

```JavaScript
fn()  // undefined
```

## 硬绑定
```JavaScript
fn.apply({},['a'])  // {}
```

忽略硬绑定， 使用软绑定
```JavaScript
fn.apply(null,['a'])  // window
```

"use strict" 严格模式
```JavaScript
fn.apply({},['a'])  // {}
fn.apply(null,['a'])  // null
```





