---
title: 【Three】Three.js光源入门之环境光
date: 2019-04-04 17:47:05
categories: 图形学
tags: [图形学, ThreeJs]
---

环境光是环境整体的光照效果， 是场景内若干光源的多次反射形成的一种亮度一致的效果


## 环境光
> AmbientLight 

它是一种基础光源， 影响整个场景的光源

环境光没有明确的光源位置， 在各处形成的亮度也是一致的。

它不会影响阴影的产生。

不能将环境光作为场景中唯一的光源。

### 在Three中使用
> THREE.AmbientLight(hex)

> add(color) 添加到当前颜色上

> clone 复制当前颜色


如果只有环境光没有物同样是看不见东西的， 所以想测试环境光， 得先创建物体， 

看一下下面两幅图:


这是正常环境光情况下的物体
![正常环境光](http://img.nixiaolei.com/three-ambientLight.png)


然后我们把环境光调暗一点:

![调暗环境光](http://img.nixiaolei.com/three-ambientLight-darker.png)


可以看到， 两张图中鲜艳的绿色明显的暗淡了

### 环境光换色

刚刚我们只是调整了亮度明暗， 现在我们试试给环境光换个颜色， 就换个红色吧， 红色的灯感觉蛮吓人的


![红色环境光](http://img.nixiaolei.com/three-ambientLight-red02.png)


我们发现， 立方体好像少了一个， 这是因为一个不透明的物体 ， 我们所看到的颜色其实是他反射出来的颜色， 而绿色立方体无法反射红色通道的颜色， 所以， 绿色立方体在我们看上去就像是隐藏了一样， 实际上是黑了， 而我们的白色， 他能反射所有颜色， 所以是他就变红了


## 如何添加
```Js
scene.add(new THREE.AmbientLight(0xff0000))
```

## 实际操作一下
大体思路， 我们创建一个面，和一个立方体， 先不投射任何的光，看看是什么效果， 然后我们最后放上环境光，看一下效果


### 第一步-创建好立方体和面


```Js
 var commonObj = common()
  var cubeGeometry = new THREE.BoxGeometry(5,5,5)
  var cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
  var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)

  // 打开立方体的阴影
  cube.castShadow = true
  cube.position.set(-4, 2.5, 0)
  
  commonObj.scene.add(cube)
  // 调整相机位置
  commonObj.camera.position.set(-25, 20, 25)
  // 镜头朝向舞台中央
  commonObj.camera.lookAt(commonObj.scene.position)


  document.body.appendChild(commonObj.renderer.domElement)
  commonObj.renderer.render(commonObj.scene, commonObj.camera)
```

此时浏览器中的效果
![无光环境](http://img.nixiaolei.com/three-ambientLight-nolight.png)

可以看到， 此时尽管我们给物体设定了他的颜色， 但我们看到的不管是面还是立方体都依旧是黑色。

### 第二步-添加光源
上帝说要有光， 我们给添加一个环境光试试

```Js
// 环境光
commonObj.scene.add(new THREE.AmbientLight()) // 默认白光
```

好， 就这行代码，就算是加上了

![有光环境](http://img.nixiaolei.com/three-ambientLight-haslight.png)

### 第三步-改变环境光颜色
环境光默认是白色， 那我们可不可以试着改变一下， 把环境光改为红色？ 是不是会很吓人

```Js
// 环境光
commonObj.scene.add(new THREE.AmbientLight(0xff0000)) // 设置红色
```


![红色环境光](http://img.nixiaolei.com/three-ambientLight-redlight.png)

此时原本白色的面，也变为了红色， 感觉好刺眼， 吓得我赶紧关了





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
<!-- 游戏帧率辅助库 -->
<script src="../stats.min.js"></script>
<script src="../SceneUtils.js"></script>
<script src="../common/common.js"></script>
<script>
  // 公共的方法
  function common() {
    var scene = new THREE.Scene()
    
    var renderer = new THREE.WebGLRenderer()
    renderer.setClearColor(new THREE.Color(0xEEEEEE, 1.0))
    renderer.setSize(window.innerWidth, window.innerHeight)
    renderer.shadowMapEnabled = true
    // 创建面
    var planeGeometry = new THREE.PlaneGeometry(60, 30)
    // 材质
    var planeMaterial = new THREE.MeshLambertMaterial({color: 0xffffff})
    var plane = new THREE.Mesh(planeGeometry, planeMaterial)
    
    plane.rotation.x = -0.5 * Math.PI
    plane.position.set(15, 0, 10)
    scene.add(plane)

    var camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000)

    return {
      scene,
      renderer,
      plane,
      camera
    }
  }


  // 开始创建

  var commonObj = common()
  var cubeGeometry = new THREE.BoxGeometry(5,5,5)
  var cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
  var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)

  // 打开立方体的阴影
  cube.castShadow = true
  cube.position.set(-4, 2.5, 0)
  
  commonObj.scene.add(cube)
  // 调整相机位置
  commonObj.camera.position.set(-25, 20, 25)
  // 镜头朝向舞台中央
  commonObj.camera.lookAt(commonObj.scene.position)

  // 环境光
  commonObj.scene.add(new THREE.AmbientLight())
  
  document.body.appendChild(commonObj.renderer.domElement)
  commonObj.renderer.render(commonObj.scene, commonObj.camera)
</script>
</html>
```







