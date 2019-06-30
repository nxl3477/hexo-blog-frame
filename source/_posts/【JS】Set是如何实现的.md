---
title: 【JS】Set是如何实现的
date: 2019-06-30 22:21:49
categories: JavaScript
tags: JavaScript
---


## 一般情况
Set一般是使用红黑树实现的, 红黑树是一种平衡查找二叉树, 它的查找时间复杂度为O(logN). 从O(N)变成O(logN), 而总体时间从O(N2)变成O(NlogN)



## V8中的实现(哈希实现)

Chrome V8的Set是哈希实现的, 他是一个哈希Set, 哈希的查找复杂度为O(1), 因此总的时间复杂度为O(N), Set/ Map都是这样


哈希的存储空间通常为数据大小的两倍. 典型的用空间换时间的算法.


## for 、Set和Map如何选型
![2019-06-30-22-30-29](http://img.nixiaolei.com/2019-06-30-22-30-29.png)



## 面试标准回答
一般情况是使用 红黑树实现的, 复杂度是 O(logN)

V8进行了优化, 复杂度降低至 O(1)









