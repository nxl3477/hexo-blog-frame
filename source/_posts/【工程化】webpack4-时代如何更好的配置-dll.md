---
title: 【工程化】webpack4 时代如何更好的配置 dll
date: 2019-11-19 10:31:21
categories: 工程化
tags:  [webpack, 工程化]
---


因为公司的pc 项目有点庞大， 每次打包构建都得1分钟左右， 实在有点无法接受， 于是尝试优化一下 webpack 看看能否提升提升相关的性能
也就有了这篇文章的由来，也是想记录一下关于webpack优化的内容

如果你去搜索 webpack 优化一定会搜索到dll相关的配置，同样也会被一串莫名其妙的webpack 搞的一头雾水， 那么我们就来简单理解一下

## 什么是dll呢
dll原本是window系统的一种优化手段 利用动态链接，大体上的操作是: 许多应用共享的代码能够切分到一个DLL中，在硬盘上存为一个文档，在内存中只需使用一个实例。 用于节省应用程序的资源和状态， 总的来说是一种用空间换时间的方案

再人话一点就是： 把程序可复用的公共代码抽出来，作为外部依赖引用

那么 Dll 在前端中的作用就是将需要打包的第三方依赖从项目中抽离出来， 单独打包一份 vender 文件，

我们的配置也就是在干这么一件事:
 * 第一次打包： 将依赖单独打包抽离， 存储在磁盘中并生成映射关系， 方便后续查找，也就是 manifest.json 文件
 * 第二次打包： 根据 manifest.json 获取之前的依赖打包缓存， 只需要打包业务相关的代码


## 简单介绍应该如何配置 dll
首先编写一个专门用来打包和更新 dll 的 webpack 配置, 保存到项目根目录中 `webpack.dll.config.js`
```js
'use strict';

const path = require('path');
const webpack = require('webpack');

module.exports = {
    mode: 'production',
    entry: {
        react: ['react', 'react-dom'],
    },
    output: {
        path: path.resolve(__dirname, '../dll'),
        filename: '[name].dll.js',
        library: '[name]_library',
    },
    plugins: [
        new webpack.DllPlugin({ 
            name: '[name]_library',
            context: __dirname,
            path: path.resolve(__dirname, '../dll/[name]-manifest.json'),
        })
    ]
};
``` 
编写好脚本必然需要有一个方法去触发他， 我们到 package.json 中编写一个 script : 
```json
"script": {
  "dll": "webpack --config=webpack.dll.config.js"
}
```

此时我们运行`npm run dll`便可生成一份打包的 dll文件



那如何建立起动态链接呢? 

上面的打包操作会产出一份 `manifest.json` 映射关系文件，它就像一个“地图”绘制着各个被打包到dll的依赖的相关信息， 那我们只要把这份映射关系文件告诉webpck ， 让他按照这个份“地图” 去寻址

所以我们要在正常的打包配置中加入这样一个 plugins, 然后利用 `add-asset-html-webpack-plugin` 将静态资源的路径插入到html中

```js
plugins: [
  new webpack.DllReferencePlugin({
    context: path.resolve(__dirname, '../'),
    manifest: path.resolve(__dirname, '../dll/react.manifest.json'),
  }),
  new AddAssetHtmlPlugin({
    filepath: path.resolve(__dirname, '../dll/_dll_react.js'),
  })
]
```





## dll 优劣
dll的缓存机制带来的打包效果是非常显著的， 能大幅减少项目的打包时间， 上面也说了dll 是一种拿空间换时间的一种方式， 可以先将项目所依赖的库提前打包出来， 存储在本地磁盘中， 然后通过mainifest.json 的映射文件， 在下次打包的时候产生链接关系， 这样就可以做到， 你把这份提前打包的dll包放置在 cdn上？ 因为他不会经常性的修改， 只需要在项目的第三方依赖需要修改的时候再去替换他即可， 然后每次打包都可以跳过打包第三方依赖，只处理业务代码， 

但其实这样也有一个问题， 那就是整个 spa 应用就会很蠢， 本来是可以利用 code split 来达到按需加载js  文件的功能， 但是这样一整吧， 不管用户访问哪个页面都会把整个作为 dll 的依赖包下载下来， 变相的增加了加载时间，  所以dll也只是推荐在开发环境使用，  生产环境还是使用 commonChunk 的方式提取公共依赖把， 虽然 commonChunk无法为你优化打包时间



## webpack4 中我们应该怎么做


同样的配置我尝试在使用了 webpack4的新项目中试了试， 发现 dll 加速太不明显了， 测试了几次感觉只优化了100毫秒， 因为webpack4  的性能足够好  （ 项目原本打包需要1.8秒上下 ）， 所以这也是 vue-cli 将 dll 这种方式弃用的原因

```
dll option will be removed. Webpack 4 should provide good enough perf and the cost of maintaining DLL mode inside Vue CLI is no longer justified.

dll 配置将会被移除，因为 Webpack 4 的打包性能足够好的，dll 没有在 Vue ClI 里继续维护的必要了。
```


那我们真的只能跟 dll 分手告别了吗? 不会的，工具只会越发展越完善， 在webpack4的时代，我们可以使用 `hard-source-webpack-plugin` ， 
只需要在webpack的plugins 中加入
```js
plugins: [
  new HardSourceWebpackPlugin()
]
```
就这仅仅一行即可， 效果拔群

就是他了， 上了这车直接提升了3分之一的速度， 太秀了吧， 硬核


并且听说等到了 webpack5 自身就会集成了


## 参考文献
> https://segmentfault.com/a/1190000020485804