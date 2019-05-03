---
title: 【Three】Three.js入门之绘制三维对象
date: 2019-04-02 09:47:58
categories: 图形学
tags: [图形学, ThreeJs]
---

* Plane ( 平面 ) 二维矩形，渲染结果是在屏幕中央有个灰色矩形
* Cube (方块) 三维立方体。
* Sphere ( 球体 ) 三维球体。
* Camera ( 相机 ) 决定视点的位置， 和最终观察结果
* Axes ( 轴 ) 辅助测试工具



## 第一步-设置场景
变量`Scene`是一个容器， 它用来保存并且跟踪我们想渲染的物体
```Js
var scene = new THREE.Scene();
```

具体内容我们后续会添加到 `scene`中

## 第二步-设置相机
`camera`用来控制我们所看到的方向角度
```Js
// 设置相机                    视角,  宽度/高度,   近距离点,  远距点
var camera = new THREE.PerspectiveCamera(50, window.innerWidth/window.innerHeight, 0.1, 1000)
// 设置坐标
camera.position.x = -30
camera.position.y = 40
camera.position.z = 30
// 设置朝向
camera.lookAt(scene.position) // 看向场景的中央
```


## 第三步-设置绘制对象
我们创建一个基于`webgl`的渲染器, 当然还有其他的渲染器( canvas ,svg渲染器等)
```Js
var renderer = new THREE.WebGLRenderer()
// 设置背景颜色
renderer.setClearColor(new THREE.Color(0xeeeeee));
// 控制渲染尺寸
renderer.setSize(window.innerWidth, window.innerHeight)
```

## 第四步-设置辅助测试工具
```Js
// 设置辅助测试工具
var axes = new THREE.AxisHelper(20)
scene.add(axes)
```


## 第五步-创建集合体

### 创建一个平面
```Js
  // 设置平面
  var planeGeometry = new THREE.PlaneGeometry(70, 50, 1, 1) //设置宽高和段度
  var planeMaterial = new THREE.MeshLambertMaterial({color: 0xcccccc}) // 设置颜色
  var plane = new THREE.Mesh(planeGeometry, planeMaterial)
  // 设置旋转角度
  plane.rotation.x = -0.5 * Math.PI;
  // 设置坐标
  plane.position.x = 15
  plane.position.y = 0
  plane.position.z = 0
  // 加入场景
  scene.add(plane)
```


### 创建一个立方体
```Js
// 设置立方体
var cubeGeometry = new THREE.CubeGeometry(4, 4, 4)  //设置宽高和段度
// 设置基础材质
var cubeMaterial = new THREE.MeshBasicMaterial({color: 0xff0000, wireframe: true}) // 设置颜色和打开线框
var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
// 设置坐标
cube.position.x = 4
cube.position.y = 3
cube.position.z = 0
// 加入场景
scene.add(cube)
```


### 创建一个球体
```Js
// 设置球面体
var sphereGemometry = new THREE.SphereGeometry(4, 20, 20)
var sphereMaterial = new THREE.MeshBasicMaterial({ color: 0x7777ff, wireframe: true })
var sphere = new THREE.Mesh(sphereGemometry, sphereMaterial)
sphere.position.x = 20
sphere.position.y = 4
sphere.position.z = -2
scene.add(sphere)
```


## 最后一步-输出到body
```Js
// 将renderer的输出挂到body
document.body.appendChild(renderer.domElement)
renderer.render(scene, camera)
```

## 最终效果
![最终效果](http://img.nixiaolei.com/2019-05-02-12-02-43.png)



## 完整代码
> git地址: https://0x9.me/ohetH


```HTML
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
<script>
    // 设置场景,
    var scene = new THREE.Scene();
    // 设置相机                      视角,  宽度/高度,   近距离点,  远距点
    var camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000)
    camera.position.x = -30
    camera.position.y = 40
    camera.position.z = 30
    camera.lookAt(scene.position) // 看向场景的中央


    // 设置绘制对象---基于Webgl的渲染器
    var renderer = new THREE.WebGLRenderer()
    renderer.setClearColor(new THREE.Color(0xeeeeee));
    // 控制渲染尺寸
    renderer.setSize(window.innerWidth, window.innerHeight)

    // 设置辅助测试工具
    var axes = new THREE.AxisHelper(20)
    scene.add(axes)

    // 设置平面
    var planeGeometry = new THREE.PlaneGeometry(70, 50, 1, 1) //设置宽高和段度
    var planeMaterial = new THREE.MeshLambertMaterial({color: 0xcccccc}) // 设置颜色
    var plane = new THREE.Mesh(planeGeometry, planeMaterial)
    // 设置旋转角度
    plane.rotation.x = -0.5 * Math.PI;
    // 设置坐标
    plane.position.x = 15
    plane.position.y = 0
    plane.position.z = 0
    // 加入场景
    scene.add(plane)

    // 设置立方体
    var cubeGeometry = new THREE.CubeGeometry(4, 4, 4)  //设置宽高和段度
    // 设置基础材质
    var cubeMaterial = new THREE.MeshBasicMaterial({color: 0xff0000, wireframe: true}) // 设置颜色和打开线框
    var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
    // 设置坐标
    cube.position.x = 4
    cube.position.y = 3
    cube.position.z = 0
    // 加入场景
    scene.add(cube)

    // 设置球面体
    var sphereGemometry = new THREE.SphereGeometry(4, 20, 20)
    var sphereMaterial = new THREE.MeshBasicMaterial({ color: 0x7777ff, wireframe: true })
    var sphere = new THREE.Mesh(sphereGemometry, sphereMaterial)
    sphere.position.x = 20
    sphere.position.y = 4
    sphere.position.z = -2
    scene.add(sphere)

    // 将renderer的输出挂到body
    document.body.appendChild(renderer.domElement)
    renderer.render(scene, camera)

</script>
</html>
```