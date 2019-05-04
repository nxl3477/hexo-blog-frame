---
title: 【Three】Three.js光源入门之点光源
date: 2019-04-04 17:47:05
categories: 图形学
tags: [图形学, ThreeJs]
---

点光源是一种单点发光照亮物体的光源

## 基础介绍
* 照射所有方向的光源，例如照明弹
* 点光源是单点发光方式
* 点光源不会产生阴影， 减少GPU 的负担



## 调用方法
```Js
THREE.PointLight(hex, intensity, distance)
```

* clone() ---- 复制当前颜色。 color 光源颜色
* intensity ---- 光照强度
* distance ---- 光源照射的距离
* position ---- 光源所在位置

















