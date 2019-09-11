---
title: 【工程化】了解webpack原理
date: 2019-09-11 22:58:30
categories: 工程化
tags:  [webpack, 工程化]
---


> webpack 的成功之处在于其loader , 但 webpack 的失败之处也出在这个loader

对于webpack 来讲， 万物都是模块， webpack 为什么可以做到万物都可以 `require`就是使用了loader,  但同时也是loader 导致了webpack 的编译速度（你懂的）， 这是为什么呢， 这里先说一下基本原理
***

webpack 在处理的时候， 会经过多个loader, 比如`babel-loader`,`less-loader`， 那如何把这些看似毫不相关的， 又是天南地北找来的， 出自不同开发者之手的loader 集合起来呢:

首先是第一个接受任务的loader, loader1 会接受代码的字符串， 然后把代码变成 Ast 进行处理， 结束以后再转为 string字符串。

也就是说，整个 loader1 经历了一下的流程:

字符串 -> Ast 静态语法树 -> 遍历Ast处理业务 -> 字符串


同样的loader2 也会经历这样一个过程！


也就是说所有loader 穿起来就是:
```
loader1 开始 -> 获取字符串 ->  Ast -> 遍历Ast处理业务 -> 字符串 -> loader1结束 -> loader2 开始 -> 获得字符串 -> Ast -> 遍历Ast处理业务 -> 字符串 -> loader 2 结束 -> loader3开始 -> ………等等
```

乍一看， 你可能会觉得， 是不是太蠢了， 为什么不等到全部loader处理完成再转换回 字符串， 还要转来转去， 原来打包时间都在这消耗掉了。
没错， 你当然不是第一个想到的， 这就是之前大火的parcel 的打包机制（虽然parcel的零配置很优秀， 但因为被webpack抄走了， 所以也渐渐势头不在）,  并且parcel还启用了多核打包:

```
string字符串 -> Ast(loader1 & loader2 & loader3) -> string 字符串  外加多核打包📦
```

一气呵成， 这感觉，多爽， 确实比webpack快多了 ，省了那么多步骤， 能不快吗


## webpack 与优化
上面提到了 parcel 的多核打包， 可我们的主场是webpack ,怎么能不提一提webpack 的多核打包呢， 

相信大家都听过或者尝试过臭名昭著的 `happypack` 多核打包插件， 但这个插件实际上在高版本的webpack 上效果略低啊

happypack 可以用，但也需要考虑下应用场景： 在需要打包的文件太少不建议， 因为多核打包也是需要消耗系统性能的， 反而会使打包变慢了

实际操作起来效果不明显


25.20