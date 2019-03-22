---
title: 利用CSS混合模式更改图片颜色
date: 2019-03-16 18:34:26
categories: CSS
tags: [css]
---
<script type="text/javascript" src="/js/src/bai.js"></script>


CSS3 新增了一个很有意思的属性 `mix-blend-mode` ，其中 mix 和 blend 的中文意译均为混合，那么这个属性的作用直译过来就是混合混合模式，当然，我们我们通常称之为混合模式。

混合模式最常见于 photoshop 中，是 PS 中十分强大的功能之一。当然，瞎用乱用混合模式谁都会，利用混合模式将多个图层混合得到一个新的效果，只是要用到恰到好处，或者说在 CSS 中利用混合模式制作出一些效果则需要对混合模式很深的理解及不断的尝试。



开始动手吧 ~

## 尝试修改图片物体颜色

首先我们创建一个Input框和一个 Img标签（其实这里我选择的图片不是很好）
```HTML
<body>
  <input type="color" />
  <img src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1553336864&di=2f41edc8eedbf09fb1d106545acaed5f&imgtype=jpg&er=1&src=http%3A%2F%2Fpic44.nipic.com%2F20140723%2F19276212_171901262000_2.jpg" alt="">
</body>
```

然后我们将图片撑满屏幕， 将Input也撑满屏幕， 将input覆盖至图片之上， 然后为input 添加上混合模式`mix-blend-mode: hue;` 
> 因为input框自带颜色， 所以当用户切换颜色时也就能不依靠JS 即时生效


```CSS
<style>
html,body, input, img {
  width: 100%;
  height: 100%;
}
input{ 
  position: absolute;
  top: 0;
  left: 0;
  mix-blend-mode: hue;
}
</style>
```


### 过程记录
原图:
![原图](http://img.nixiaolei.com/2019-03-16-18-43-09.png)

添加上了混合模式(变灰是因为color选择器默认是黑色):
![添加上了混合模式](http://img.nixiaolei.com/2019-03-16-18-44-01.png)

切换颜色中:
![切换颜色中](http://img.nixiaolei.com/2019-03-16-18-45-53.png)

切换后: 

![切换后](http://img.nixiaolei.com/2019-03-16-18-46-30.png)

虽然有点丑但我们成功了~


## 更多可能性

利用整个混合模式的特性， 我们还能做出更多的东西， 比如利用三原色实现彩色loading、 万花筒等等炫酷动画， 可以看看参考文献中的例子， 非常的不错


## 参考文献
> https://www.cnblogs.com/coco1s/p/6829372.html