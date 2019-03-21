---
title: 【css-doodle】 css web-component入门
date: 2019-03-16 20:36:22
categories: CSS
tags: [CSS, web-component, 特效]
---
<script type="text/javascript" src="/js/src/bai.js"></script>

首先祭出 css-doodle的官网， 真的老厉害了， 使用它能够轻松的实现各种炫酷特效
> https://css-doodle.com/


`<css-doodle />`基于 Shadow DOM v1和 Custom Elements v1。您可以立即在最新的Chrome，Safari和Firefox上使用它，而无需使用polyfill。
该组件将根据其中的规则（纯CSS）生成div网格。您可以使用CSS轻松操作这些单元格，以获得图形模式或动画图形。限制是CSS本身的限制。



既然它这么牛逼， 那怎么用呢？

我们一起来搭几个demo

## css-doodle入门
> 此处用到的语法或api有不了解的 请看页面下方的“释义” 区域

动手过程中需要注意， 不要在`css-doodle`内部写注释， 会导致样式无效


首先搭建好运行`css-doodle`的环境
```HTML
<!-- 引入CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/css-doodle/0.5.1/css-doodle.min.js"></script>

<!-- 页面上加入 css-doodle -->

<css-doodle>
<!-- 我们将在这里写代码 -->
</css-doodle>
```

此时如果我们打开页面， 你会发现是空空如也的， 因为这个元素既没有宽高也没有背景颜色， 我们当然看不到。

现在我们给他加点料。

```HTML
<css-doodle>
  width: 100px;
  height: 100px;
  background: red;
</css-doodle>
```

此时我们再看看页面， 就出现了一个`100 x 100`的红色小方块，

![红色方块](http://img.nixiaolei.com/2019-03-16-21-22-36.png)


好，这个相当于hello world 级别的练习就完成了， 


我们再来点难的：

**我想要一个10 x 10 个的小方块组成的大正方形!**

正好我们可以借此尝试使用一下它提供的API


```
<css-doodle>
  :doodle {
    @grid: 10x10 / 500px;
  }
  background: #60569e;
</css-doodle>
```

出现了！ 我们要的 10 x 10 个小方块组成的大正方， 并且还把颜色换成了骚骚的紫色


![10x10的大正方](http://img.nixiaolei.com/2019-03-16-21-28-29.png)


好既然实现了， 那现在需求升级了

**那我要10个长方形怎么办， 并且我还要长短不一的！**

我去，还要长短不一， 咋整呢

别慌

机制的你一定可以在官网找到有一个获取当前小方块顺序的的参数 
`@index` 严格的来说他会返回当前单元格的索引值

既然有方法了那我们也有思路了， 二话不说开始动手。

```HTML
<!-- 首先把 10 x 10 小方块变为10个长方形 -->
<!-- 然后根据 @index 获取当前单元格的索引 -->
<css-doodle>
  :doodle {
    @grid: 1x10 / 61.8vmin;
  }
  background: #60569e;
  @size: calc(@index() * 1vmin);
</css-doodle>
<!-- 根据 index 这个索引值乘以高度 -->
```

duang!

这就是我们想要的结果了， 虽然还有点不完美， 其实每个紫色都是一个正方形， 但所在的单元格是长方形没跑了


![10个长方形](http://img.nixiaolei.com/2019-03-16-21-45-14.png)


我们再给他加上`border-radius: 50%;` 它就变成10个球了


![10个球](http://img.nixiaolei.com/2019-03-16-21-50-12.png)


此时需求又来了

**甲方：可不可以弄10个光环？还是渐变的那种?**

行吧， 试试喽谁怕谁

我们可以举一反三， 利用`@index`这个好东西来修改`hsla`产生不同的颜色， 当然修改`rgba` 也是可以的

```
<!-- 根据index 每个的单元格递增20的色调， 再调整出不同的透明度 -->
<css-doodle>
  :doodle {
    @grid: 1x10 / 61.8vmin;
  }
  border-color: hsla(
    calc(20 * @index()), 70%, 68%, calc(3 / @index() * 0.8)
  );
  @size: calc(@index() * 10%);
  border-style: dashed;
  border-radius: 50%;
</css-doodle>
```

好， 甲方爸爸， 你的需求我做到了



![渐变光环](http://img.nixiaolei.com/2019-03-16-22-11-38.png)


**甲方: 还不够，我要的是同心圆**

我。。。。。

得嘞，您说是啥就是啥

同心圆， 你第一反应想到的是不是使用`position: absolute;` ? 
不 ，那太low 了
我们只需要 `@place-cell: center;` 这个参数就搞定了， 它会将每个单元格都摆放至中间

```HTML
<css-doodle>
  :doodle {
    @grid: 1x10 / 61.8vmin;
  }

  @place-cell: center;
  <!-- ...省略之前的代码 -->
</css-doodle>
```
然后去看一下效果， 卧槽

![我把背景色调成深色方便大家观看](http://img.nixiaolei.com/2019-03-16-22-20-13.png)



### 进阶
基础学的差不多了， 其他的一些api有了上面的参照都应该思路了吧

既然是进阶篇， 我们就做点难的， 我们看看能不能利用这些特性和 CSS动画结合一下

我们先让光环变粗一点， 让他呈现扇叶的形状

```
border-width: calc(@index() * 10px);
```


然后让我们用上 `keyframes` ， 为他添加动画

为每个单元格注册几个css变量（css的新特性），  

* 变量`--speed`控制动画的持续时间，也就是速度。
* `--start` 控制当前光环的起始角度
* `--direction`: 控制方向， 因为`turn`表示一圈， 所以 `1turn` 就是正的转一圈,`-1turn` 就是反着转一圈， 利用`@pick`方法来随机取一个值




```HTML
<css-doodle>
  <!-- ...省略之前的代码 -->

  <!-- 动画执行时间在20秒 - 40秒之间取值-->
  --speed: @rand(20s, 40s);
  <!-- 360度里取随机数 -->
  --start: @rand(360deg);
  <!-- 顺时针 或 逆时针 -->
  --direction: calc(var(--start) + @pick(1turn, -1turn));

  animation: demo var(--speed) linear infinite;

  @keyframes demo {
    form {
      <!-- 填入起始角度 -->
      transform: rotate(var(--start));
    }
    to {
      <!-- 随机顺时针和逆时针旋转 -->
      transform: rotate(var(--direction));
    }
  }
</css-doodle>
```

看看效果


![随机旋转效果](http://img.nixiaolei.com/KUXh4tYGe4.gif)


炫酷不？

能不能再酷一点？

可以， 我们再给他添一笔， 

给他加上一个自定义的贝塞尔

贝塞尔可以去这个网站，根据需要生成
> http://cubic-bezier.com/#1,.25,.83,.67


![贝塞尔](http://img.nixiaolei.com/2019-03-16-23-11-46.png)



然后我们你所需要的贝塞尔去更改`animation` 的参数
```HTML
animation: demo var(--speed) cubic-bezier(0,.88,.42,.99)  infinite;
```

根据贝塞尔曲线完成了加速减速的动画效果


![添加了贝塞尔曲线的动画](http://img.nixiaolei.com/ApPKe0brCf.gif )



😎cool~

还有更多更酷的特效等着大家去探索~




**切记`css-doodle` 中不能写注释，大家拷贝的时候记得删掉**

## 释义
* `:doodle` 选择器会创建一个`display: grid;` 的布局
* `@grid`提供布局， 用法为 `@grid: 行 x 列 /  总宽高` 
* `@index` 返回当前单元格的索引
* `hsla` 此色彩模式与HSL相同，只是在HSL模式上新增了Alpha透明度。
  * H：Hue(色调)。0(或360)表示红色，120表示绿色，240表示蓝色，也可取其他数值来指定颜色。取值为：0 - 360
  * S：Saturation(饱和度)。取值为：0.0% - 100.0%
  * L：Lightness(亮度)。取值为：0.0% - 100.0%
  * A：Alpha透明度。取值0~1之间。
* `@place-cell` 调整单元格相对于网格放置。
* `@pick`： 从给定列表中随机选择一个值。
* `turn`: 转、圈（Turns）。一个圆共1圈 ( 90deg = 100grad = 0.25turn ≈ 1.570796326794897rad )
  * `1turn` 就是1圈






## 完整的代码
```HTML
<css-doodle>
  :doodle {
    @grid: 1x10 / 61.8vmin;
  }
  @place-cell: center;
  border-color: hsla(
    calc(20 * @index()), 70%, 68%, calc(3 / @index() * 0.8)
  );
  border-width: calc(@index() * 10px);
  @size: calc(@index() * 10%);
  border-style: dashed;
  border-radius: 50%;
  --speed: @rand(20s, 40s);
  --start: @rand(360deg);
  --direction: calc(var(--start) + @pick(1turn, -1turn));
  animation: demo var(--speed) cubic-bezier(0,.88,.42,.99)  infinite;

  @keyframes demo {
    form {
      transform: rotate(var(--start));
    }
    to {
      transform: rotate(var(--direction));
    }
  }
</css-doodle>
```