---
title: 【Three】Three.js入门之几何和网格对象
date: 2019-04-03 17:48:05
categories: 图形学
tags: [图形学, ThreeJs]
---

## Geometry

Three.js 中自带了很多的几何体， 我们可以直接使用他们的方法就可以轻松的创造出一个几何体，

比如:

创建一个面
```Js
new THREE.PlaneGeometry()
```

创建一个立方体
```Js
new THREE.CubeGeometry()
```

我们可以看到都带有`Geometry` 这个单词， 翻译过来也是“几何体”的意思
其实这个`THREE.Geometry`是所有几何对象的基类( 简称 geom )


### vertices顶点数组
> 翻译： 顶点

一个集合体我们可以看成是由几个顶点构成的， 比如一个立方体， 他就是由8个在空间中的顶点构成的。 

在Three中， `geom.vertices`表示几何体的顶点， 它是一个数组

所以我们不但可以利用`Three`已有的几何体， 还可以自己提供顶点， 自己来创建一个集合体


### faces 侧面
> 翻译: 面

`geom.faces` 表示几何体的侧面



### 动手创建一个立方体

上面我们说了， 一个立方体， 由8个顶点 和 6个面构成， 如果让我们自己来实现， 我们就需要手动来创建下
#### 定义8个顶点

```Js
var vertices = [
  new THREE.Vector3(1, 3, 1),
  new THREE.Vector3(1, 3, -1),
  new THREE.Vector3(1, -1, 1),
  new THREE.Vector3(1, -1, -1),
  new THREE.Vector3(-1, 3, -1),
  new THREE.Vector3(-1, 3, 1),
  new THREE.Vector3(-1, -1, -1),
  new THREE.Vector3(-1, -1, 1)
]
```

#### 定义6个面
因为在Three 中，可以理解为万物都由 三角形构成， 所以要想有正方形， 需要通过三角形创建( 以前支持直接创建正方形， 后来被删除 )

```Js
// 增加 6个面
var faces = [
  // 一个正方形由两个三角形构成
  new THREE.Face3(0, 2, 1),
  new THREE.Face3(2, 3, 1),

  new THREE.Face3(4, 5, 6),
  new THREE.Face3(6, 7, 5),

  new THREE.Face3(4, 5, 1),
  new THREE.Face3(5, 0, 1),
  
  new THREE.Face3(7, 6, 2),
  new THREE.Face3(6, 3, 2),
  
  new THREE.Face3(5, 7, 0),
  new THREE.Face3(7, 2, 0),
  
  new THREE.Face3(1, 3, 4),
  new THREE.Face3(3, 4, 4),
]
```


#### 生成几何物体
刚刚我们配置的只是所需的数据， 现在我们要做的是将他合并成一个完整的几何体

```Js
// 创建几何体
var geom = new THREE.Geometry()
geom.vertices = vertices
geom.faces = faces
geom.computeFaceNormals()
```


#### 添加材质


![控制台报错](http://img.nixiaolei.com/sceneUtils-error.png)
如果控制带出现这个错误， 说明`SceneUtils` 已经被Three 独立拆除去了， 所以要想使用需要单独引入

```Js

// 增加材质
var materials = [
  new THREE.MeshLambertMaterial({
    opacity: 0.5, 
    color: 0x44ff44, 
    transparent: true
  }),
  new THREE.MeshBasicMaterial({
    color: 0x000000, 
    wireframe: true
  })
]

// 构成物体
var mesh = new THREE.SceneUtils.createMultiMaterialObject(geom, materials)
mesh.children.forEach(
  (e) => {
    e.castShadow = true
  }
)

```


#### 渲染场景
加入到场景渲染一下我们刚刚创建的立方体
```Js
scene.add(mesh)
renderer.render(scene, camera)
```

此时，我们可以看到画布上真的出现了一个立方体
![渲染场景结果](http://img.nixiaolei.com/three-gemo-mesh.png)





## 网格对象的属性和函数
* position 决定该对象相对于父对象的位置。
* rotation 设置对象绕任何一个轴的旋转弧度
* scale沿 x, y 和 z轴缩放对象
* translateX x轴平移
* translateY y轴平移
* translateZ z轴平移


使用这些api移动一下我们刚刚创建的立方体吧

### 移动一下立方体

```Js
// 平移
mesh.position.x = 10
mesh.position.y = 10
mesh.position.z = 1
// // 等同于
// mesh.position.set(10, 10, 1)
```

此时效果
![移动立方体](http://img.nixiaolei.com/2019-05-03-23-41-13.png)

### 再旋转一下立方体
```Js
// // 旋转
mesh.rotation.x = 2
mesh.rotation.y = 2
mesh.rotation.z = 2
```

此时效果
![旋转立方体](http://img.nixiaolei.com/three-mesh-rotation.png)

### 缩放一下立方体
我们可以针对某一个轴进行缩放， 这里我们缩放x轴， 其他轴同理

```Js
// x 轴方向放大4倍
mesh.scale.x = 4
```

此时效果

![立方体缩放效果](http://img.nixiaolei.com/three-mesh-scale.png)




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
<script>
    // 设置场景,
    var scene = new THREE.Scene();
    // 设置相机                      视角,  宽度/高度,   近距离点,  远距点
    var camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000)
    camera.position.x = -30
    camera.position.y = 40
    camera.position.z = 10
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


    // 将renderer的输出挂到 body
    document.body.appendChild(renderer.domElement)
    renderer.render(scene, camera)

    // 定义一个方块
    // 1.定义8个顶点
    // 2. 6个面
    var vertices = [
      new THREE.Vector3(1, 3, 1),
      new THREE.Vector3(1, 3, -1),
      new THREE.Vector3(1, -1, 1),
      new THREE.Vector3(1, -1, -1),
      new THREE.Vector3(-1, 3, -1),
      new THREE.Vector3(-1, 3, 1),
      new THREE.Vector3(-1, -1, -1),
      new THREE.Vector3(-1, -1, 1)
    ]

    // 增加 6个面
    var faces = [
      // 一个正方形由两个三角形构成
      new THREE.Face3(0, 2, 1),
      new THREE.Face3(2, 3, 1),

      new THREE.Face3(4, 5, 6),
      new THREE.Face3(6, 7, 5),

      new THREE.Face3(4, 5, 1),
      new THREE.Face3(5, 0, 1),
      
      new THREE.Face3(7, 6, 2),
      new THREE.Face3(6, 3, 2),
      
      new THREE.Face3(5, 7, 0),
      new THREE.Face3(7, 2, 0),
      
      new THREE.Face3(1, 3, 4),
      new THREE.Face3(3, 4, 4),
    ]

    // 创建几何体
    var geom = new THREE.Geometry()
    geom.vertices = vertices
    geom.faces = faces
    geom.computeFaceNormals()

    // 增加材质
    var materials = [
      new THREE.MeshLambertMaterial({
        opacity: 0.5, 
        color: 0x44ff44, 
        transparent: true
      }),
      new THREE.MeshLambertMaterial({
        color: 0x000000, 
        wireframe: true
      })
    ]

    // 构成物体
    var mesh = new THREE.SceneUtils.createMultiMaterialObject(geom, materials)
    mesh.children.forEach(
      (e) => {
        e.castShadow = true
      }
    )


    // 平移
    mesh.position.x = 10
    mesh.position.y = 10
    mesh.position.z = 1
    // // 等同于
    // mesh.position.set(10, 10, 1)
    
    // // 旋转
    mesh.rotation.x = 2
    mesh.rotation.y = 2
    mesh.rotation.z = 2

    // // x 轴方向放大4倍
    mesh.scale.x = 4


    scene.add(mesh)
    renderer.render(scene, camera)

</script>
</html>
```