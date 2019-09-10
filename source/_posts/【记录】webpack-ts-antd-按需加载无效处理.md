---
title: 【记录】webpack + ts + antd 按需加载无效处理
date: 2019-05-10 00:15:41
categories: 记录
tags: 记录
---


webpack加 babel 本来搭配 babel-plugin-import 可以实现插件的按需引入， 但是时代在变化啊， 现在流行ts 了， 你一定会想在你的项目中加入它， 你也一定会搜索到 ‘ts-loader’ 的种种，  但效果不尽如人意， 使用了'ts-loader' 之后， 你会发现， 按需引入怎么配置都失败了，

于是你一定会尝试这样
```
{
    test: /\.tsx?$/,
    exclude: /node_modules/,
    use: [
        loader: 'babel-loader',
        loader: 'ts-loader',
    ]
}
```

先把ts 转成 es6, 然后用`babel`把ES6 转为 es5

但现在不是那种上古洪荒时代了， babel 统一添加， 让我们可以不需要在编译两轮了


在 babel 7 中，我们使用新的 @babel/preset-typescript (其集成了 @babel/plugin-transform-typescript)

我们的 .babelrc 配置将变成这样：

```
{
  "presets": [
    "@babel/env",
    "@babel/react",
    "@babel/typescript"
  ]
}
```


参考地址:
> https://github.com/frontend9/fe9-library/issues/23

> https://www.jishuwen.com/d/2MbR/zh-tw














