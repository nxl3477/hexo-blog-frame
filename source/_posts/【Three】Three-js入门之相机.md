---
title: 【Three】Three.js入门之相机
date: 2019-05-03 23:51:33
categories: 图形学
tags: [图形学, ThreeJs]
---


有两种相机
* 近交投影
* 正交投影

## 近交投影
和真实世界中我们所看到的物体一样， 近大远小
![近交投影](http://img.nixiaolei.com/three-camera-a.png)


### 透视投影照相机
> THREE.PerspectiveCamera(fov, aspect, near, far)

![透视投影照相机](http://img.nixiaolei.com/perspectiveCamera.png)

* fov ---- 视野宽度
* aspect ---- 长宽比， 推荐使用 window.innerWidth / window.innerHeight
* near ---- 近裁面， 推荐值 0.1
* far ---- 远裁面， 推荐值 1000，  值太大影响性能， 值太小场景显示不全


## 正交投影
> 建模类软件使用的比较多


正交投影就像老师在黑板上画的立方体，保证三维世界平行的线到了二维世界也是平行的， 不存在近大远小的概念，

### 正交投影相机
> THREE.OrthographicCamera(left, right, top, bottom, near, far)  // near far决定远近范围

* left ---- 左边界
* right ---- 右边界
* top ---- 上边界
* bottom ---- 下边界
* near ---- 近裁面
* far ---- 远裁面
* camera.lookAt ---- 设置目标点


在Three中， 创建正交投影相机的参数决定了所看到的`视景体`, `视景体`内部的物体才能显示在屏幕上，视景体外部的就会被裁剪掉



图中灰色区域就是`视景体`
![正交投影相机](http://img.nixiaolei.com/OrthographicCamera.png)




## 摄影机效果比对
说概念总是比较抽象， 我们来实际操作一下，

大概思路就是， 首先将立方体铺满平面， 然后我们使用定时器来切换相机类型 ， 以此来区别出两种相机的效果


### 第一步----将立方体铺满平面

利用我们之前设定的面的宽高， 计算出一列应该有多少立方体， 一行应该有多少立方体， 然后将其排列好， 添加入场景

```Js
// 将立方体铺满屏幕
var cubeGeometry = new THREE.BoxGeometry(4,4,4)
for ( var j=0; j < planeGeometry.parameters.height / 5 ;j++) {  // 面高 50
  for ( var i=0; i < planeGeometry.parameters.width / 5 ; i++) { // 面宽 70
    // 随机生成
    var rnd = Math.random() * 0.75 + 0.25
    // 添加材质
    var cubeMaterial = new THREE.MeshLambertMaterial()
    // 添加颜色
    cubeMaterial.color = new THREE.Color(rnd, 0, 0)
    // 创建立方体
    var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
    // 设置立方体的位置
    cube.position.x = -(planeGeometry.parameters.width / 2) + 2 +(i * 5)
    cube.position.y = 2
    cube.position.z = -(planeGeometry.parameters.height / 2) + 2 +(j * 5)
    // 立方体加入场景
    scene.add(cube)
  }
}
```

### 第二步----用键盘控制相机角度
每当按下左右键时， 就调整相机的y 轴方向的旋转角度， 然后重新绘制场景

```Js
// 绑定键盘事件
document.onkeydown = function(e) {
  switch(e.keyCode) {
    case 37:
      camera.rotation.y += 0.02
      render()
      break;
    case 39:
      camera.rotation.y -= 0.02
      render()
      break;
  }
}
function render() {
  renderer.render(scene, camera)
}
render()
```



### 第三步----利用定时器切换摄像机的模式

利用定时器切换 相机的模式，然后就会在下次一用户按下键盘时被渲染出来

```Js
// 用于切换相机模式
var controls = new function () {
  this.perspective = "Perspective"
  this.switchCamera = function () {
    if( camera instanceof THREE.PerspectiveCamera ) {
      console.log('Orthographic')
      this.perspective = 'Orthographic' 
      camera = new THREE.OrthographicCamera (
        window.innerWidth / -16,
        window.innerWidth / 16,
        window.innerHeight / -16,
        window.innerHeight / 16,
        -200, 
        500
      )
      camera.position.x = -20
      camera.position.y = 60
      camera.position.z = 50
      camera.lookAt(scene.position)
    }else {
      console.log('Perspective')
      this.perspective = "Perspective"
      camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
      camera.position.x = -20
      camera.position.y = 60
      camera.position.z = 50
      camera.lookAt(scene.position)
    }
  }
}
// 切换相机模式
setInterval(() => {
  controls.switchCamera()
}, 2000)
```


### 查看效果
随着我不断地按下键盘的方向键， 画布被重新渲染， 我们可以看到两种相机的不同效果在来回切换

![两种摄影机模式](http://img.nixiaolei.com/camera-doubule-mode.gif)




