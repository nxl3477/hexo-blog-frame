---
title: 【JS】初识WebComponents
date: 2019-11-20 11:41:30
categories: JavaScript
tags: [JavaScript, webComponents]
---





# Web Components

* Custom Elements
  * 定制元素， 也就是自定义的元素，像vue的组件
* HTML Imports
  * 支持import 方式引用组件
* HTML Templates
  * 可以实现把dom结构写好
* Shadow DOM
  * 沙箱dom ， 外部的css不会影响其


## Custom Elements
提供⼀种⽅式让开发者可以⾃定义 HTML 元素，包括特定的组成，样式和行为。

```
class ButtonHelloElement extends HTMLButtonElement {
    constructor() {
      super()
      this.addEventListener('click', () => {
        alert('hello world')
      })
    }
  }
  customElements.define('button-hello', ButtonHelloElement, {
    extends: 'button'
  })
</script>
<button id="button-hello">hello world</button>
```

## HTML Imports
HTML Imports 是一种在 HTML 中引⽤以及复⽤其他的HTML ⽂档的⽅式。这个 Import 很漂亮，可以简单理解为我们常⻅的模板中的 include 之类的作⽤。

听说这个标准后来被废弃了
```
<link rel="import" href=“/components/header.html">
const link = document.querySelector('link[rel=import]')
const header = link.import;
const pulse = header.querySelector(‘div.logo');
//获取 import 的 HTML 的 document
const d = document.currentScript.ownerDocument
```

## HTML Templates
用过 handlebars 的⼈都知道有这么⼀个东⻄: `<script id="template" type="text/x-handlebars-template"></script>`那么 HTML Templates 便是把这个东⻄官⽅标准化，提供了了⼀个 template 标签来存放以后需要但是暂时不渲染的 HTML 代码。

```
<template id="template"><p>Smile!</p></template>
<script>
  let num = 3;
  const fragment = document.getElementById('template').content.cloneNode(true);
  while (num-- > 1) {
    fragment.firstChild.before(fragment.firstChild.cloneNode(true));
    fragment.firstChild.textContent += fragment.lastChild.textContent;
  }
  document.body.appendChild(fragment);
</script>
```


## Shadow DOM

Shadow DOM 最本质的需求是需要⼀个隔离组件代码作⽤域的东⻄西，例如我组件代码的 CSS 不能影响其他组件之类的，⽽iframe ⼜太重并且可能有各种奇怪问题。旨在提供⼀种更好地组织⻚⾯元素的⽅式，来为⽇趋复杂的⻚⾯应⽤提供强⼤⽀持，避免代码间的相互影响。
```
const div = document.getElementById('id')
const shadowRoot = div.createShadowRoot()
const span = document.createElement('span')
span.textContent = 'hello world'
shadowRoot.appendChild(span)
```

x-foo::shadow > span 可以匹配到 #top 元素
x-foo /deep/ span 可以匹配到 #not-top 和 #top 元素
:host(.foo) 匹配 `<x-foo>` 元素









