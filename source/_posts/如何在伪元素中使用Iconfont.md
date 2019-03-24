---
title: 如何在伪元素中使用Iconfont
date: 2019-03-24 14:45:47
categories: CSS
tags: [css]
---


## 把iconfont 中的样式拷到 伪元素上
```CSS
.iconfont{
  font-family:"iconfont" !important;
  font-size:16px;font-style:normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}
```

## 砍掉unicode 前面的字符

## 如下 即可
```CSS
&::after{
  content: '\e60e';

  font-family:"iconfont" !important;
  font-size:16px;font-style:normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}
```



<div style='display: none;'>
谢谢最可爱的贝玺
</div>















