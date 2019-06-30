---
title: 【算法】理解Dom-diff
date: 2019-06-23 21:48:48
categories: 算法
tags: DomDiff
---


## 算法复杂度
比较两棵DOM树的差异是 Virtual Dom 算法最核心的部分， 这也是所谓的Virtual Dom的 diff算法。 两个树的完全的diff算法是一个时间复杂度为 O（n^3） 的问题。 但是在前端当中， 你很少会跨越层级地移动Dom元素。 所以 Virtual Dom只会对同一个层级的元素进行对比。 下面的div指挥和同一层级的div对比， 第二层级的智慧跟第二层级对比。 这样算法复杂度就可以达到O(n)。


![2019-06-23-21-55-03](http://img.nixiaolei.com/2019-06-23-21-55-03.png)


## 对比方式
实际的代码中， 会对新旧两棵树进行一个深度优先的遍历，这样每个节点都会有一个唯一的标记， 在深度优先遍历的时候， 每遍历到一个节点就把该节点和新的树进行对比。 如果有差异的话就记录到一个对象里面。


![2019-06-23-21-57-26](http://img.nixiaolei.com/2019-06-23-21-57-26.png)




## 实践一下

所谓的`dom-diff` 抛去具体的dom处理逻辑， 其核心思想还是比较简单的， 其实就是一个深度优先遍历。

我们就来尝试实现一下一个简单的dom-diff

首先
### 模拟dom结构
以面向对象的编程方式， 构建出一个dom节点所必要的属性

```js
class Element {
  constructor(type, props, children) {
    this.type = type
    this.props = props
    this.children = children
  }
}
```

然后我们再实现一个创建虚拟dom节点的快捷方式

```js
function createElement (type, props, children) {
  return new Element(type, props, children)
}
```

既然有了创建方法， 那现在就可以试着创建几个dom了， 
```js
let virtualDom1 = createElement("ul", {
  class: "list"
}, [
  createElement("li", { class: "item"}, ["1"]),
  createElement("li", { class: "item"}, ["2"]),
  createElement("li", { class: "item"}, ["3"])
])

let virtualDom2 = createElement("ul", {
  class: "list-new"
}, [
  createElement("li", { class: "item"}, ["a"]),
  createElement("li", { class: "item"}, ["444"]),
  createElement("li", { class: "item"}, ["c"])
])
```

这是两串模拟`ul > li`的代码， 相信聪明的你可以看的出来


### 对比差异
既然构建好了简单的虚拟dom， 接下来要做的无非就是比对了

在对比差异的过程中， 我们还要记录最终的差异结果， 把当前修复批次记录下来， 用于之后同一处理

开始比对之前， 我们要先搞清楚整体的逻辑， 我们应该比对哪些差异？

1. 标签类型的差异， 有可能是`p`， 有可能是`div`， 还有可能是纯文本
2. dom的属性改变了 ， 有可能是class变了， 有可能是自定义属性变了
3. dom的内容改变了， 这就是最基本的变化了， dom的内容发生了改变


```js

import _ from './util.js'
// 生成的补丁包
let patchs = {}
// 记录
let globalIndex = 0

// dom-diff的主要入口函数
function diff(oldTree, newTree) {
  dfswalk(oldTree, newTree, globalIndex)
  return patchs
}
// 深度优先遍历 -- 递归
function dfswalk(oldTree, newTree, globalIndex){
  // 记录当前批次的修改队列
  let currentPatchs = []
  // 如果旧节点是文本
  if( _.isString(oldTree) ) {
    // 如果新树也是字符串 并且 但是不和新树相同
    if(_.isString(newTree) && oldTree !== newTree) {
      // 将当前的更改追加入当前需要修改的队列中
      currentPatchs.push({
        type: "TEXT",
        text: newTree
      })
    }

    // 如果类型相同的
  } else if( oldTree.type === newTree.type ) {
    // 调用对比属性的函数
    diffProps(oldTree.props, newTree.props)
    // 比节点 => 尾调用优化
    diffChildren(oldTree.children, newTree.children)
  }
  // 如果本次检测到了修改 把每一次diff的结果放到patchs
  if(currentPatchs.length > 0) {
    // 记录到对应的节点上sdcxz
    patchs[globalIndex] = currentPatchs
  }
}

// 对比两个dom的 属性
function diffProps() {
  // 对比props

}
// 负责循环新旧domde的子节点， 以旧节点的索引去匹配去匹配相应位置的新节点
function diffChildren(oldChildrens, newChildrens) {
  // 遍历旧dom ,对比相同索引的新dom
  oldChildrens.forEach((child, index) => {
    // 被循环后又去调用新旧dom的比对
    dfswalk(child, newChildrens[index], ++globalIndex)
  })
}



export {
  diff
}


```




## Vue 对dom-diff的优化

vue 在对比 oldTree 和 newTree 时会分别给新旧树的对比中会分配两个指针，分别是`oldStart`、`oldEnd` 、 `newStart`、`newEnd` ， 然后 start 指针和 end 指针都会在每次对比后相互靠近， 直至相遇， 具体对比规则如下

![2019-06-29-21-05-31](http://img.nixiaolei.com/2019-06-29-21-05-31.png)



1. 先对比头头、 尾尾是否一致
2. 对比头尾、 尾头， 如果相同，则头尾先互相拷贝插入， 然后删除旧的
3. 如果 `newStart` 和 `oldStart` 相对比， `newStart`有但`oldStart`没有此节点， 则将 `newStart` 对应的节点插入至 `oldStart`节点的前面， 并且将 `newStart`指针前进一格， 而`oldStart`指针不动， 直至找到匹配
![2019-06-29-20-58-31](http://img.nixiaolei.com/2019-06-29-20-58-31.png)
4. 如果`oldEnd` 和 newEnd 不同时， 则将oldEnd 的元素先打上删除标记， 然后前进一格，  newEnd 元素则停留在原处，直至找到匹配
![2019-06-29-21-22-26](http://img.nixiaolei.com/2019-06-29-21-22-26.png)
5. 对比阶段， 对比类型， 如果类型不同就直接放弃没必要继续对比了


最终start 与 end 交汇后， 遍历一遍 oldTree ,将标记为删除的节点删除掉


### Vue 为什么需要key
首先， 如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试修复/再利用相同类型元素的算法。使用key，它会基于key的变化重新排列元素顺序，并且会移除key不存在的元素。

并且， diff算法就可以快速切正确的找到此节点， 找到正确的位置插入新的节点，

降低节点查找时间， 当有插入节点需要时， 有Key 的话就可以 类似于索引访问的形式直达目标节点，  而不是遍历一遍所有节点来查找目标节点


**利用key的小技巧**
它也可以用于强制替换元素/组件而不是重复使用它。当你遇到如下场景时它可能会很有用:

完整地触发组件的生命周期钩子
触发过渡

```Vue
<transition>
  <span :key="text">{{ text }}</span>
</transition>
```


**总结**

key是为了降低diff时的节点查找复杂度， 并且如果节点的key都不相同或者是说key都变了，那就直接判定为dom发生变化， 渲染新的