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


## 最终效果
添加完阴影后，我们刷新一下浏览器， 阴影出来了， 只是有些粗糙

![阴影](http://img.nixiaolei.com/2019-05-03-17-44-10.png)



## 完整代码
> git地址: https://0x9.me/ohetH


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
    // 打开阴影
    renderer.shadowMapEnabled = true


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

    plane.receiveShadow = true
    // 加入场景
    scene.add(plane)

    // 设置立方体
    var cubeGeometry = new THREE.CubeGeometry(4, 4, 4)  //设置宽高和段度
    // 设置基础材质
    var cubeMaterial = new THREE.MeshLambertMaterial({color: 0xff0000}) // 设置颜色和打开线框
    var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
    // 设置坐标
    cube.position.x = 4
    cube.position.y = 3
    cube.position.z = 0
    cube.castShadow = true

    // 加入场景
    scene.add(cube)

    // 设置球面体
    var sphereGemometry = new THREE.SphereGeometry(4, 20, 20)
    var sphereMaterial = new THREE.MeshLambertMaterial({ color: 0x7777ff })
    var sphere = new THREE.Mesh(sphereGemometry, sphereMaterial)
    sphere.position.x = 20
    sphere.position.y = 4
    sphere.position.z = -2
    sphere.castShadow = true 
    scene.add(sphere)

    /*
    *
    *  添加材质和灯光
    *
    * */

    // 设置点光源

    var spotLight = new THREE.SpotLight(0xffffff);
    // 设置位置
    spotLight.position.set(-40, 60, -10)
    spotLight.castShadow = true
    // 添加进点光源
    scene.add(spotLight)

    






    /*
    *
    * 输出
    *
    * */

    // 将renderer的输出挂到body
    document.body.appendChild(renderer.domElement)
    renderer.render(scene, camera)




</script>
</html>
```


