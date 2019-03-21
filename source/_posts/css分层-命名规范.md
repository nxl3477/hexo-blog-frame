---
title: css分层&命名规范
date: 2019-03-16 23:32:55
categories: CSS
tags: [CSS, 命名规范]
---
<script type="text/javascript" src="/js/src/bai.js"></script>

为什么要分层? 

* CSS有语义化的命名约定和CSS层的分离， 将有助于它的可扩展性， 性能的提高和代码的组织管理。
* 避免大量的样式、 覆盖、 权重和很多！important, 分好层可以让团队命名统一规范， 方便维护。
* 有责任感地去命名你的选择器


有哪些分层理论? 
* SMACSS
* BEM
* SUIT
* ACSS
* ITCSS



## BEM

BEM和SMACCS非常类似， 主要用来如何给项目命名。一个简单命名更容易让别人一起工作。 比如选项卡导航是一个块(Block)， 这个块里的元素是其中标签之一（Element）, 而当前选项卡是一个修饰状态( Modifier )

* block - 代表了更高级别的抽象或组件
* block__element - 代表.block的后代， 用于形成一个完整的.block的整体。
* block--modifier - 代表.block 的不同状态或不同版本。
* 修饰符使用的是_, 子模块使用的是__符号。 ( 不同一个-的原因是因为CSS单词连接 )

```HTML
  <!-- container 是大容器 -->
  <div class="container">
    <div class="menu">
      <div class="menu--item menu--item__active">首页</div>
      <div class="menu--item">新闻</div>
      <div class="menu--item">消息</div>
    </div>
  </div>
```