---
title: Js与CSS变量操作
date: 2019-03-16 17:47:07
categories: CSS
tags: [JavaScript, css]
---
<script type="text/javascript" src="/js/src/bai.js"></script>



就目前来说CSS变量已经不是什么新鲜的东西了， 渐渐的我们可以在浏览器中尝试使用它， 和SCSS、 LESS 等预处理器不同的是， CSS的变量机制是浏览器天生支持的， 并且浏览器还对其做了优化， 减少了我们使用JS 去操作CSS变量造成的重绘和重排

一起来看看如何操作吧~
> 变量虽好， 不可滥用哦

### 定义一个简单的变量
```CSS
/* 定义css 变量 */
:root {
  --backgroundColor: pink;
}

body {
  background-color: var(--backgroundColor);
}
```

### 创建一个按钮
```HTML
<body>
  <button id="btn">点我改变背景</button>
</body>
```

### 获取root元素并绑定按钮的点击事件
```JavaScript
<script>
var root = document.documentElement

btn.onclick = function() {
  // 将变量修改为天蓝色
  root.style.setProperty('--backgroundColor', 'skyblue')
}
</script>
```


### 结果对比

点击前
![按钮点击前](http://img.nixiaolei.com/2019-03-16-17-52-45.png)

按钮点击后
![按钮点击后](http://img.nixiaolei.com/2019-03-16-17-53-39.png)



非常Nice~


利用这个特性实现更多的可能吧！