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



## 测试点光源
测试方法和大体上和环境光是一样的， 但是点光源和环境光不同的是， 他有个特点， 它能产生阴影

那我们就创建一个点光源和几个几何物体， 顺便移动一下点光源， 看看阴影是如何变化的

### 创建立方体
```Js

// 立方体
var cubeGeomtry = new THREE.BoxGeometry(5,5,5)
var cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
var cube = new THREE.Mesh(cubeGeomtry, cubeMaterial)
cube.position.set(0, 6, 0)
commonObj.scene.add(cube)
```

### 创建球体

```js
// 球体
var sphereGeometry = new THREE.SphereGeometry(4, 20, 30)
var sphreMaterial = new THREE.MeshLambertMaterial({ color: 0x7777ff })
var sphere = new THREE.Mesh(sphereGeometry, sphreMaterial)
sphere.position.set(20, 6, 0)
commonObj.scene.add(sphere)
```


### 创建点光源
```Js
// 添加点光源
var pointLight = new THREE.PointLight(0xffffff)
pointLight.position.set(-40, 60, -10)
commonObj.scene.add(pointLight)
```


### 让点光源动起来
```JS
function render() {
  requestAnimationFrame(render)
  pointLight.position.z += 0.2
  commonObj.renderer.render(commonObj.scene, commonObj.camera)
}

render()
```

### 最终效果

我们可以看到， 随着点光源的不断发生位置变动， 我们可以看到阴影发生逐渐的发生变化

![](http://img.nixiaolei.com/yA1pla0mDn.gif)


## 完整代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>第一个ThreeJs</title>
</head>
<style>
  body {
    margin: 0;
    overflow: hidden;
  }
</style>
<body>
</body>
<script src="../three.js"></script>
<script src="../common/common.js"></script>
<!-- 游戏帧率辅助库 -->
<script src="../stats.min.js"></script>
<script src="../SceneUtils.js"></script>
<script src="../common/common.js"></script>
<script>
  var commonObj = common()

  // 立方体
  var cubeGeomtry = new THREE.BoxGeometry(5,5,5)
  var cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
  var cube = new THREE.Mesh(cubeGeomtry, cubeMaterial)
  cube.position.set(0, 6, 0)
  commonObj.scene.add(cube)
  
  // 球体
  var sphereGeometry = new THREE.SphereGeometry(4, 20, 30)
  var sphreMaterial = new THREE.MeshLambertMaterial({ color: 0x7777ff })
  var sphere = new THREE.Mesh(sphereGeometry, sphreMaterial)
  sphere.position.set(20, 6, 0)
  commonObj.scene.add(sphere)
  
  
  commonObj.camera.position.set(-25, 30, 25)
  commonObj.camera.lookAt(commonObj.scene.position)




  // 添加点光源
  var pointLight = new THREE.PointLight(0xffffff)
  pointLight.position.set(-40, 60, -10)
  commonObj.scene.add(pointLight)

  function render() {
    requestAnimationFrame(render)
    pointLight.position.z += 0.2
    commonObj.renderer.render(commonObj.scene, commonObj.camera)
  }

  render()
  document.body.appendChild(commonObj.renderer.domElement)
</script>
</html>
```



