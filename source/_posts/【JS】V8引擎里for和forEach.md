---
title: 【JS】V8引擎里for和forEach
date: 2019-05-11 22:13:30
categories: JavaScript
tags: JavaScript
---


一般是for 循环比较快,  但是V8中每个数组自带的方法一定程度上是对数组数量级的优化, 在一定数量的数据下, forEach 要优于 for 循环, 所以看场景选择吧, 具体请看v8源码














