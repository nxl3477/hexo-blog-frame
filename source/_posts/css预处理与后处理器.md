---
title: css预处理与后处理器
date: 2019-03-16 12:20:43
categories: CSS
tags: [css, PostCss]
---
<script type="text/javascript" src="/js/src/bai.js"></script>

预处理器在前两年是非常流行的技术， 但是这两年发展的却不怎么有起色， 因为人们渐渐发先即使代码经过了预处理的的编译， 还要经过后处理， 并且PostCss 的崛起拥有了预处理器的功能， 可以完美代替预处理的工作， 并且可以做到让人们使用下一个版本的css

一旦预处理器阶段代码出现污染，预处理器的事就算是白干了， 不如直接交给后处理器，  所以使用预处理器的意义越来越小了

## 预处理器具有的能力
* 变量
* 混合(Mixin) Extend
* 嵌套规则
* 运算
* 函数
* Namespaces & Accessors (命名空间和访问器)
* scope
* 注释


## 后处理器的能力

* CSS 压缩 CLEAN-CSS
* 自动添加浏览器前缀 Autoprefixer
* CSS更加美观排序 CSScomb
* Rework取代stylus 后处理器发热
* 前后通吃的 PostCss





## 他们是如何处理的CSS

### 预处理器

预处理器， 如： `sass` 它会先将他的语法转换成 AST--抽象语法树（Abstract Syntax Tree ）, AST是程序的一种中间表示形式， 然后遍历 AST 这棵树， 将对应想要的结果生成为CSS 

### PostCss

PostCss 改变了之前对CSS的处理流程， 它提供了一套插件的机制， 对于PostCss来说，自己是一套空的工程，它为插件提供了CSS数据， 根据配置的不同插件， 产生不同的功能

![PostCss处理流程](http://img.nixiaolei.com/2019-03-16-13-23-52.png)

