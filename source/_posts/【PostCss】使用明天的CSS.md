---
title: 【PostCss】使用明天的CSS
date: 2019-03-16 13:30:24
categories: CSS
tags: [css, PostCss]
---
<script type="text/javascript" src="/js/src/bai.js"></script>


通过PostCss作为CSS的后处理器， 可以根据浏览器的市场份额， 选择性的将一些最新的CSS新特性编译成浏览器支持的语法

当然国内的情况有点不一样

## 新特性举例

比如这个CSS变量:
他存储了媒体查询的规则， 在多个媒体查询的情况下就不用重复写了
```CSS
@custom-media --viewport-medium (width <= 50rem);

@media (--viewport-medium) {
  body { font-size: calc(var(--fontSize) * 1.2); }
}

```

那这个属性浏览器兼容不理想怎么办？ 别担心， 交给PostCss, 经过处理， 我们可以看到，生成了一个规规矩矩的媒体查询代码

```CSS
@media (max-width: 50rem) {
  body { font-size: 1.2rem; }
}
```

## 更多演示
> 编译前后以 ------ 分割


自定义选择器
```CSS
@custom-selector :--heading h1, h2, h3, h4, h5, h6;
:--heading { margin-top: 0 }
/* ------------------------------------ */
h1,h2,h3,h4,h5,h6 { margin-top: 0 }
```

自动一倍图(1X)和二倍图(2X)
```CSS
/* image-set function */
.foo {
  background-image:
    image-set(
      url(img/test.png) 1x,
      url(img/test-2x.png) 2x
    );
}

/* ------------------------------------ */

/* 默认状态 */
.foo {
    background-image:
      url(img/test.png);
}

/* 满足 设备上物理像素和设备独立像素( device-independent pixels (dips) )的比例  */
@media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi) {
.foo {
    background-image:
      url(img/test-2x.png);
}
}
```


`any-link`, 补齐标签的剩余的所有状态， 这是为了防止某些样式不生效
```CSS
a {
color: var(--highlightColor);
transition: color 1s; /* autoprefixed ! */
}
a:hover { color: gray(255, 50%) }
a:active { color: rebeccapurple }
a:focus { background-color: rgb(255 153 0 / 33%); outline: 3px solid hsl(1turn 60% 50%); }
a:any-link { color: color(var(--highlightColor) blackness(+20%)) }

/* --------------------------------- */

a {
  color: rgb(89, 185, 204);
  -webkit-transition: color 1s;
  transition: color 1s; /* autoprefixed ! */
}
a:hover { color: rgba(255, 255, 255, 0.5) }
a:active { color: #639 }
a:focus { 
  background-color: rgba(255, 153, 0, .33); 
  outline: 3px solid hsl(360, 60%, 50%); 
}
a:link,a:visited { color: rgb(89, 142, 153) }
```


还有很多， 不一一举例了， 比如：
* 各个浏览器的滤镜`filter`兼容
* 选择器正则
* css嵌套

## 如何让PostCS支持嵌套
> 本环境基于webpack

npm地址:
* https://www.npmjs.com/package/postcss
* https://www.npmjs.com/package/postcss-loader


```CSS
.container {
  /* 一定要带上 & 才会编译 */
  & .menu {
    height: 40px;
    background: sandybrown; 
    & .menu--item {
      height: 40px;
      color: skyblue;
      & .menu--item__active {
        color: pink;
      }
    }
  }
}
```




首先在项目的根目录中创建一个文件`postcss.config.js`

相关依赖安装就不说了， 真的缺了也会报错提示的

我们将会在里面做`PostCSS`的相关配置

```JavaScript
module.exports = {
  plugins: [
    require('postcss-nested')
  ]
}
```

然后接下来是配置 Loader ，

进入webpack 配置css loader 的地方， 我们在css loader 后面加上PostCss 的loader
```JavaScript
rules: [
  {
    test: /\.css$/,
    use: [
      {
        loader: 'css-loader',
      },
      // --------------新增区域---------------
      {
        loader: 'postcss-loader',
        options: {
          ident: 'postcss',
          plugins: (loader) => [
            require('postcss-preset-env')({
              /* 这里要改成 0  否则不一定编译嵌套 */
              stage: 0,
              features: {
                'nesting-rules': true
              }
            })
          ]
        }
      }
      // --------------新增区域---------------
    ]
  }
]
```

像这样，记得把 `stage` 这个参数修改为 0 ， 因为PostCss 会根据这个参数分为好几个阶段，阶段太高它是不处理嵌套的。


我们运行一下看看

```CSS
.container {
  /* 一定要带上 & 才会编译 */
}
.container .menu {
  height: 40px;
  background: sandybrown
}
.container .menu .menu--item {
  height: 40px;
  color: skyblue
}
.container .menu .menu--item .menu--item__active {
  color: pink;
}
```

大功告成😄~

想知道有用的PostCSS的插件大纲，可以在本站内搜索: `CSS插件` 






## 相关的网站
> PostCss: https://postcss.org/
> css next: http://cssnext.io/

