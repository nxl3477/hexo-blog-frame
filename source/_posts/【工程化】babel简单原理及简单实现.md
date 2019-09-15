---
title: 【工程化】babel简单原理及简单实现
date: 2019-09-14 21:36:45
categories: 工程化
tags:  [babel, 工程化, Ast]
---


我们知道babel 的原理是通过转换为Ast的方式， 进行一系列的处理， 然后最终得到了我们看到的代码。

这就是它强大之处。

如果让你来实现一个babel 这样的转换代码工具， 你会如何去做呢？

是不是首先会想到使用正则呢， 但实际上， 正则无法很好的满足需求， 这是因为： 
1. 需要针对各个类库编写相关正则
2. 当类库内部实现改变则正则也要改变
3. 正则的匹配需要区分注释情况， 有写代码被注释掉如果也被正则匹配了那就会影响实现， 比如`requireJs`就是使用正则匹配的， 导致注释掉相关方法还是调用了


综上所诉， 最终我们的解决方案应该是 AST!

如何实现一个简单的babel 呢： 

我们需要有将代码转换为 AST 的工具， 处理AST 的工具， AST 转回代码的工具！

让我们来做一个 es6 变量转为es5变量的小工具， 让我们开始动手吧

让我们先 从字符串转为AST：

```js
const esprima = require('esprima')
const estraverse = require('estraverse')
const code = ` const view = {
    a: 3,
    init: () => {
      view.a = 5
    },
    render: () => {

    }
  }
`
const ast = esprima.parse(code)
```

让我们看看上面构建的这段代码字符串会被转换为什么



![2019-09-14-22-50-42](http://img.nixiaolei.com/2019-09-14-22-50-42.png)


看到了结果， 我们也看到了我们需要的关键字`const` ， 是不是我们只要把它改为var 就可以了呢， 哈哈， 动手试试吧

```js
const esprima = require('esprima')
const estraverse = require('estraverse')
const escodegen = require('escodegen')
// mock 一段代码
const code = ` const view = {
  a: 3,
  init: () => {
    view.a = 5
  },
  render: () => {

  }
}
`
// 将代码转为 ast
const ast = esprima.parse(code)
console.log(ast)
// 遍历ast 修改相关操作
estraverse.traverse(ast, {
  enter(node) {
    if( node.type === "VariableDeclaration" ) {
      node.kind = 'var'
    }
  }
})
// 得到经过ast处理的 js 代码 
const reg_code = escodegen.generate(ast)
console.log('--------- 输出结果 ---------------')
console.log(JSON.stringify(reg_code, null,4))

```


此时的输出:

![2019-09-14-23-00-49](http://img.nixiaolei.com/2019-09-14-23-00-49.png)

可以看到最下面的字符串， const 被成功替换为了 var ， 完美！

一个最小最简陋的babel 就这么实现了！


