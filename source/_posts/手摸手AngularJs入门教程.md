---
title: 手摸手AngularJs入门教程
date: 2019-03-16 12:00:36
categories: JavaScript
tags: [Angular]
---
<script type="text/javascript" src="/js/src/bai.js"></script>

AngularJs 是一款来自Google 的前端JavaScript框架， 也是SPA框架。 AngularJs框架的体积非常小， 但是设计理念和功能却非常强大， 极大地简化前端开发的负担， 它快速成为了JavaScript的主流框架， 帮助开发者从事web开发。

这里我们用AngularJs7的练手吧

## 起步
> 请确保电脑已安装了`Node.js`

首先是全局安装脚手架
```shell
npm install -g @angular/cli
```
安装好后查看一下版本
> ng --version

![查看版本](http://img.nixiaolei.com/2019-03-17-15-54-41.png)


确认`ng`命令存在后就可以开始创建我们的第一个 Angular 项目了
```shell
# 创建项目  project 替换为你的项目名称
ng new project
```

当你输入命令后他会问你需要的配置，  

这里附上我的选择， 大家可以参照

1. 你是否需要添加路由？ ---- yes
2. 选择你喜欢的css 风格 ---- SCSS

![选择配置](http://img.nixiaolei.com/2019-03-17-15-36-01.png)


选择好后， 就会进入漫长的安装了
![安装中](http://img.nixiaolei.com/2019-03-17-15-39-53.png)

安装好后， cd 进入到项目所在的目录， 运行 `ng serve` 命令， 并打开`http://localhost:4200/` 查看我们第一个 `Angular`项目
```shell
# 进入你的项目目录
cd project

# 启动项目
ng serve

# 如果你需要 可以生产环境运行
ng serve --prod --aot 
```

## 基本的路由配置
> 如果你不知道路由是什么， 你只需要先理解路由帮助我们实现切换页面的功能
因为之前我们在构建项目时就选择了加入`路由`， 因此也省去了很多配置过程， 

现在，我们来看看怎么用吧， 

首先想使用路由， 得需要再创建一个"页面"（这里指组件）， 这样才能通过切换看出是否实现了路由， 

**那如何创建一个组件呢？**

Angular 给我们提供了非常多的创建命令， 组件也可以一键的生成

我们来创建一个 `Demo` 组件
> ng g c Demo

等待片刻， 安装好后

我们发现项目文件中的 `app` 目录下多了一个文件夹
![Demo组件创建](http://img.nixiaolei.com/2019-03-17-17-01-55.png)

里面有配套的
* 模板文件
* 样式文件
* 测试文件
* ts 脚本文件


既然有了组件， 那我们现在需要为这个路由分配相应的访问路径

进入 `app/app-routing.module.ts` 这个文件

我们首先导入刚刚创建的`Demo`组件， 再 `routes`数组中，分配一个用于访问的`path`, 并和导入的`DemoComponent`关联上

如下:

```JavaScript
// ------------新增区域------------
import { DemoComponent } from './demo/demo.component';
// ------------新增区域------------
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';


const routes: Routes = [
  // -----------新增区域-------------
  {
    path: "demo",
    component: DemoComponent
  }
  // ------------新增区域------------
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```


然后进入 `app/app.component.html` 文件中找到 `<router-outlet></router-outlet>`

刚才配置的路由就会显示在这个标签所在的位置，
为了减少一点干扰，我们把没有用的信息删除

!component19html
切换后](http://img.nixiaolei.com/2019-03--17-17-27-25.png app.我只留了一张图片,

然后我们进入 `app/demo/demo.component.html` 文件中

原本是
```HTML
<p>
  demo works!
</p>
```

我们改成明显一点的内容
> 千万注意dom结构别错了， dom结构错误会造成白屏，坑的是没有错误信息

```HTML
<div>
  <h1>我是Demo 页面</h1>
</div>
```

我们看看是否配置成功了


首先，刚进入， 只有一张图片

![根路由](http://img.nixiaolei.com/2019-03-17-17-34-37.png)


让我们切换至`/demo`

我们可以看到， 刚刚为demo组件编写的文字成功显示出来了， 并且是位于图片下方， 这跟我们之前 `app/app.component.html` 中所做的配置一样

![切换至demo](http://img.nixiaolei.com/2019-03-17-17-35-14.png)



--------------------待完善-------------------------




























## 常用的命令
> 把 XXX 替换成你要的名字

* ng g cl xxx // 创建一个新类
* ng g c xxx // 创建一个新组件
* ng g d xxx // 创建一个新指令
* ng g e xxx // 创建一个新枚举
* ng g m xxx // 创建一个新模块
* ng g p xxx // 创建一个新管道
* ng g s xxx // 创建一个新的指服务


 
## 参考文献
> https://github.com/angular/angular-cli/wiki

