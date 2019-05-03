---
title: 【Three】Three.js入门之添加材质和灯光
date: 2019-04-03 17:16:47
categories: 图形学
tags: [图形学, ThreeJs]
---

本文基于"Three.js入门之绘制三维对象" 文章之后


## 材质与光源
可以对光产生反应的材质
* MeshLamberMaterial 材质
* MeshPhongMaterial 材质
* 加入阴影效果





## 添加灯光

```Js
var spotLight = new THREE.SpootLight(0xffffff);
// 设置位置
spotLight.position.set(-40, 60, -10)
// 将光源添加进场景
scene.add(spotLight)
```




当我们将三维物体的所有材质都替换为`MeshLamberMaterial` 时, 效果如下



![替换材质后效果](http://img.nixiaolei.com/2019-05-03-17-30-33.png)


我们看到这些三维物体上确实都有了些反光的效果

但是， 有反光还不够， 既然有光那肯定要有阴影， 但是绘制阴影是比较耗费性能的，所以`Three.js` 默认不会打开， 但是我们想绘制也是比较容易的

## 给各个物体打开阴影

首先给绘制对象打开阴影设置
```Js
renderer.shadowMapEnabled = true
```

然后将几个三维对象分别打开
```Js
// 打开面的阴影
plane.receiveShadow = true

//........

// 打开立方体的阴影
cube.castShadow = true

//........

// 打开球面体的阴影
sphere.castShadow = true 
```

最后还需要给光源定义一下阴影， 让他可以生成阴影

```Js
spotLight.castShadow = true
```