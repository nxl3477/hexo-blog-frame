---
title: 【工程化】记录webpack容易记错的几个点
date: 2019-11-23 11:55:03
categories: 工程化
tags:  [webpack, 工程化]
---


## filename 与 chunkFilename

他们都是 webpack 配置文件里， `output` 中所提供的字段之一，


#### filename
负责配置就是我们平时打包时候所看到的产出文件的名字, 比如最常见的 `bundle.js`

通常我们的配置可能是
```js
output: {
  filename: '[name]-[hash].js'
}
```

#### chunkFilename

它负责控制webpack对代码进行 code splite 后产出的可按需加载文件的文件名， 有的时候我们不会去填写， 但也没有关系， webpack 会根据你 `filename`字段的配置来推断出来, 会将你设置的 `[name]` 替换为 `[id]`, 根据上面的`filename`配置的话也就是变成了 `[id]-[hash].js`



> [文档地址](https://webpack.docschina.org/configuration/output/#output-chunkfilename)



## webpackPrefetch、webpackPreload 和 webpackChunkName


他们都是属于魔法注释

具体用法都像下面这样, 以此来告诉webpack 针对某个模块应该怎么处理

```js
import(/* webpackChunkName: "myname" */ './index.js');
```


#### webpackChunkName
webpack4 目前的 chunk 生成命名还没有那么的智能， 只能生成一串 `id`, 比如`0.as123sads.js` 这样类似的文件， 但是我们的chunk 文件怎么可能只有一个， 如果是按照`id` 来的话岂不是一个文件发生变化， 那可能影响到其他的文件的id 也发生变化吗， 那可太蠢了

所以 `webpackChunkName` 就是为了解决这个问题， 可以让我们指定一个chunk 文件名称， 通过魔法注释告诉 `webpack` 这个chunk应该叫什么


#### webpackPrefetch

翻译过来是`预拉取`

其特点是 被`prefetch`的 `chunk` 会在浏览器首屏资源加载完成后， 在浏览器的空闲时间进行下载

会在页面中生成如下代码
```html
<link rel="prefetch" as="script">
```

#### webpackPreload
`预加载` 是预加载当前导航下可能需要资源

其特点是会和浏览器首屏资源并行下载， 立即开始请求




## 臭名昭著的 hash、chunkhash、contenthash

都是生成hash ， 但是计算的依据及效果确是大有不同
#### hash

hash 的计算是跟整个项目的构建相关，每次构建都会有一个相同的hash


#### chunkhash

根据不同的 chunk 文件分析生成对应的 hash ， 这个hash 和chunk 是一对一的关系， 这也解决了每次打包一个chunk的修改会导致所有chunk的hash都发生变化的问题


#### contenthash

这个就比较好理解了， 根据文件内容生成特有的 hash, 不管你是css 还是 js 只要你这份文件没有改变， 那hash就不会改变， 所以使用这种 hash 打出来的包会hash 各不相同， 是一个比较好用的hash 方式



