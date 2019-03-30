---
title: 【Vue】源码解析
date: 2019-03-18 09:50:34
categories:
tags:
---

现在三大框架风起云涌， JQuery老大哥的光辉不再， 使前端成为了各路诸侯的兵家必争之地， 

当然作为一名优质的前端，光跟风学框架是肯定不行的， 要知其然而知其所以然

要了解MVVM的本质原理， `virtual Dom`和 `Diff`算法带来的好处以及坏处

拒绝盲目跟风


## 📃diffDom优劣
现在很多人都说 `Vue`, `React` 多牛， `Diff`算法快，不用操作`Dom`。

只能无语。

`Diff`算法不是不需要操作`Dom`,  而是不需要开发者去操作`Dom`了， `Diff`算法其实不快， 就算使用了`virtual Dom`,  还得花实现把真实`Dom` 转换为 `virtual Dom` 再去比对， 这远远没有`js`直接 `getElementById`直达目标来的快

那Diff算法不快为什么还要用呢?

`Diff`算法其实是给那些比较随意的新手开发者准备的

看这个例子

```HTML
<!-- 原本的dom -->
<ul id="ul">
  <li>a</li>
  <li>a</li>
  <li>a</li>
</ul>
```
```JavaScript
// 拿到结果不管三七二十一把旧dom全替换了
$.get('/api', (res) => {
  var _HTML = ""
  for(var i=0; i< res.length; i++){
    _HTML = "<li>" + res[i] +"</li>"
  }
  $("#ul").html(_HTML)
})
```

如果是原始的`Dom`操作， 有很多小白会像这个例子一样， 不管`Dom`需不需要更新， 他都把`ajax`返回的请求全部跑一边，生成`HTML`模板， 然后把原本的所有`li`都删了，  再把新的模板放进去， `Dom`少还看不出来， 如果`Dom`多了呢， 上千的`Dom`， 这顿操作一下就玩炸了。

况且网站优化原则就是尽量减小`Dom`操作， 如果是有经验的开发者， 会选择找到有变化的位置，使用`append`插入





