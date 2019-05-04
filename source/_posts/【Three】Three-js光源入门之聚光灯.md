---
title: 【Three】Three.js光源入门之聚光灯
date: 2019-04-04 17:47:05
categories: 图形学
tags: [图形学, ThreeJs]
---

聚光灯是一特殊的光源， 聚光灯能朝一个方向投射出锥形的光线。

## 基础特性
* 具有锥形效果的光源， 能够朝着一个方向投射光线
* 最常用到的光源， 它可以产生阴影
* 锥形效果， 类似电筒光照效果

### 光照效果样例

![光照效果样例](http://img.nixiaolei.com/three-SpotLight.png)

## 使用方式
> THREE.SpotLight(hex, intensity, distance, angle, exponent)

`castShadow` ---- 如果设置为`true`, 这个光源就会产生阴影。
target ---- 决定光照方向。
angle ---- 光照的角度， 默认值是 `Math.PI / 3`


## 实践一下
按照惯例， 我们测试灯光需要有几何物体才能体现出来， 因为聚光灯是中心最亮， 逐渐向外变暗， 所以我们创建几个立方体， 然后想办法摆动一下聚光灯的位置和角度看看具体效果

### 第一步-创建立方体
```JS
// 立方体
var cubeGeometry = new THREE.BoxGeometry(5,5,5)
var cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
// 打开立方体的阴影
cube.castShadow = true
cube.position.set(-4, 2.5, 0)
commonObj.scene.add(cube) // 调整相机位置
```

### 第二步-创建球体
```Js
// 圆球
var sphereGeometry = new THREE.SphereGeometry(4, 20, 30)
var sphreMaterial = new THREE.MeshLambertMaterial({ color: 0x7777ff })
var sphere = new THREE.Mesh(sphereGeometry, sphreMaterial)
sphere.position.set(20, 6, 0)
commonObj.scene.add(sphere)
```

### 第三步-创建聚光灯
创建聚光灯并将聚光灯的目标调整为刚才创建的 球体

```Js
 // 聚光灯光源
var spotLight = new THREE.SpotLight('#ffffff')
spotLight.position.x = -40
spotLight.position.y = 60
spotLight.position.z = -12
// 选择照射目标
sphere.target = sphere
// 加入场景
commonObj.scene.add(spotLight)
```

### 第四步-用键盘控制聚光灯的角度及位置
键盘方向的上下键控制聚光灯的角度，左右键控制聚光灯的位置

```JS
// 设置初始角度
var angleNUM = 3
document.onkeydown = function (e) {
  // 上键 控制聚光灯角度
  switch(e.keyCode) {
    case 38:
      angleNUM += 1
      break;
      // 下键
    case 40: 
      angleNUM -= 1
      if( angleNUM < 1 ) {
        angleNUM = 1
      }
      break
    // 左右键控制聚光灯位移
    case 37:
      spotLight.position.x += 2
      break
    case 39:
      spotLight.position.x -= 2
      break
  }
}
```


### 第五步-实时渲染
在每一帧时根据当前的情况调整聚光灯的角度

```js
function render() {
  window.requestAnimationFrame(render)
  commonObj.renderer.render(commonObj.scene, commonObj.camera)
  spotLight.angle = Math.PI / angleNUM
}
```



### 最终效果

该图中， 前半段我先按了上下键来调整聚光灯角度， 后半段我按了左右键来控制聚光灯位置

![聚光灯效果](http://img.nixiaolei.com/three-juguangdeng-keymove.gif)


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
  var cubeGeometry = new THREE.BoxGeometry(5,5,5)
  var cubeMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 })
  var cube = new THREE.Mesh(cubeGeometry, cubeMaterial)
  // 打开立方体的阴影
  cube.castShadow = true
  cube.position.set(-4, 2.5, 0)
  commonObj.scene.add(cube) // 调整相机位置
  
  // 圆球
  var sphereGeometry = new THREE.SphereGeometry(4, 20, 30)
  var sphreMaterial = new THREE.MeshLambertMaterial({ color: 0x7777ff })
  var sphere = new THREE.Mesh(sphereGeometry, sphreMaterial)
  sphere.position.set(20, 6, 0)
  commonObj.scene.add(sphere)


  commonObj.camera.position.set(-25, 20, 25) // 镜头朝向舞台中央
  commonObj.camera.lookAt(commonObj.scene.position)

  // 聚光灯光源
  var spotLight = new THREE.SpotLight('#ffffff')
  spotLight.position.x = -40
  spotLight.position.y = 60
  spotLight.position.z = -12
  // 选择照射目标
  sphere.target = sphere
  commonObj.scene.add(spotLight)


  function render() {
    window.requestAnimationFrame(render)
    commonObj.renderer.render(commonObj.scene, commonObj.camera)
    spotLight.angle = Math.PI / angleNUM
  }

  // 设置初始角度
  var angleNUM = 3
  document.onkeydown = function (e) {
    // 上键 控制聚光灯角度
    switch(e.keyCode) {
      case 38:
        angleNUM += 1
        break;
        // 下键
      case 40: 
        angleNUM -= 1
        if( angleNUM < 1 ) {
          angleNUM = 1
        }
        break
      // 聚光灯位移
      case 37:
        spotLight.position.x += 2
        break
      case 39:
        spotLight.position.x -= 2
        break
    }
  }


  render()
  document.body.appendChild(commonObj.renderer.domElement)
</script>
</html>
```
