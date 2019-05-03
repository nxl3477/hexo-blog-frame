---
title: 【Three】Three.js入门之添加动画
date: 2019-04-03 17:47:05
categories: 图形学
tags: [图形学, ThreeJs]
---

本文基于"Three.js入门之添加材质和灯光" 文章之后


## 帧率辅助库
> 在官网下载到的three文件中的 `/examples/js/libs/stats.min.js` 可以找到

这个是一个three 开发者之一写的库， 可以给我提供检测动画流畅度的数据， 引入方式和`three`一样


如何使用呢， 

### 创建一个dom

我们给他预留一个`dom`
```html
<body>
  <div id="stats-output"></div>
</body>
```

### 初始化统计对象
```Js
// 初始化统计对象
function initStats() {
  // 创建实例
  var stats = new Stats()
  // 设置模式， 0 显示 fps, 1 检测渲染时间
  stats.setMode(0) 
  // 样式设置
  stats.domElement.style.position = 'absolute'
  stats.domElement.style.left = "0px"
  stats.domElement.style.top = "0px"
  // 加入刚才的dom
  document.getElementById('stats-output').appendChild(stats.domElement)
  return stats
}
```

### 在每一帧动画期间更新
因为这里我们的动画使用的是`requestAnimationFrame`, 所以我们在每次调用该方法时， 去更新一下统计对象即可

```Js
function render() {
  // 更新统计对象
  stats.update()

  // 主循环
  requestAnimationFrame(render)
  // 渲染当前的场景
  renderer.render(scene, camera)
}
```

### 查看一下结果
这些都做好以后，打开浏览器， 左上角就可以看到我们刚刚创建的统计对象了

![统计对象](http://img.nixiaolei.com/2019-05-03-20-55-25.png)



## 创建动画
因为js定时器的种种问题， 计时不准确，  无法预估浏览器算力等等问题， 所以`requestAnimationFrame`
是我们做动画时的首选， 它可以根据浏览器的具体繁忙情况自动控制帧率

### 创建主循环
先把之前的最后一步的渲染操作放入`requestAnimationFrame` 这个主循环， 

```Js
function render() {
  stats.update() // 上面👆使用的帧率统计
  // 回调传入自身
  requestAnimationFrame(render)
  renderer.render(scene, camera)
}


 // 将renderer的输出挂到body
document.body.appendChild(renderer.domElement)
render()
```

这样，我们的主循环就创建好了， 但是还不够， 如果你刷新浏览器， 你看到的依旧是静止的， 因为你的代码没有任何变化， 所以， 我们就尝试着让他发生一些变化


### 让正方体动起来
我们让正方体旋转起来， 我们在每一帧都修改一下这个正方体的 `x,y,z` 坐标位置

```Js
function render() {
  stats.update() // 上面👆使用的帧率统计

  // -------转动方块--------
  cube.rotation.x += 0.02
  cube.rotation.y += 0.02
  cube.rotation.z += 0.02
  // ----------------------

  requestAnimationFrame(render)
  renderer.render(scene, camera)
}
```

就这三行代码就可以了， 让我们看看现在的效果


![旋转立方体](http://img.nixiaolei.com/J2oeAVFEnQ.gif)

我们可以看到， 立方体运动行径诡异啊， 但是很cool


### 让球动起来

让球动起来也很简单， 但是我们想让他动的稍微炫酷一点， 让他跳来跳去， 这就需要用到一点数学知识了， 利用三角函数我们实现一下球的弹跳

我们在函数外记录了一个 `Step` 用来每次增长， 然后球的`x, y` 就会每次都根据三角函数的曲线规律来造成一高一低的效果， 而且是抛物线

```Js
var step = 0;
function render() {
    stats.update()
    // 转动方块
    cube.rotation.x += 0.02
    cube.rotation.y += 0.02
    cube.rotation.z += 0.02
    
    // --------球体跳跃------------
    step += 0.04;
    sphere.position.x = 20 + ( 10 * Math.cos( step ))
    sphere.position.y = 2 + ( 10 * Math.abs( Math.sin(step) ))
    // ---------------------------

    requestAnimationFrame(render)
    renderer.render(scene, camera)
}
```

看一下现在的效果图
![球体运动](http://img.nixiaolei.com/three-ball-action.gif)





## 全部代码
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
    <div id="stats-output"></div>
</body>
<script src="../three.js"></script>
<!-- 游戏帧率辅助库 -->
<script src="../stats.min.js"></script>
<script>
    // 增加统计功能
    var stats = initStats()

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
    * 引入动画
    *
    * */

    var step = 0;

    function render() {
        stats.update()
        // 转动方块
        cube.rotation.x += 0.02
        cube.rotation.y += 0.02
        cube.rotation.z += 0.02
        
        // 球体跳跃
        step += 0.04;
        sphere.position.x = 20 + ( 10 * Math.cos( step ))
        sphere.position.y = 2 + ( 10 * Math.abs( Math.sin(step) ))

        requestAnimationFrame(render)
        renderer.render(scene, camera)
    }
 









    /*
    *
    * 输出
    *
    * */

    // 初始化统计对象

    function initStats() {
        var stats = new Stats()
        stats.setMode(0) // 0 显示fps, 1 检测渲染时间
        stats.domElement.style.position = 'absolute'
        stats.domElement.style.left = "0px"
        stats.domElement.style.top = "0px"
        document.getElementById('stats-output').appendChild(stats.domElement)
        return stats
    }



    // 将renderer的输出挂到body
    document.body.appendChild(renderer.domElement)
    render()



</script>
</html>


```




















