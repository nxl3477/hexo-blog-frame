---
title: vuecli3脚手架环境变量
date: 2019-04-24 14:44:02
categories: Vue
tags: Vue
---

## 环境变量
> 环境变量, 在开发和生产环境中会经常使用到

### 举一个例子
> 在开发过程中我们使用一个本地的接口      
> 但是在我们打包上线的时候, 我们会使用线上的接口      
> 这时候我们就可以通过环境变量来配置


### 如何创建环境变量
> 进入脚手架根目录 , 新建一个 .env 文件  , 这就是一个环境变量文件     

**.env**              优先级最低的配置文件, 两种环境都可以用     
**.env.development**  开发环境, 时优先使用的环境变量     
**.env.production**   生产环节, 打包时使用的环境变量     


#### 环境变量语法
> VUE_APP_衔接上一个名字='你的变量'

如: 
> VUE_APP_URL='https://baidu.com'



### 如何在组件中获取环境变量
> process.env.VUE_APP_URL     
> 然后挂载到当前组件的data 上 ,如下

```
data(){
    return {
        url: process.env.VUE_APP_URL
    }
}

```


<div style='display: none;'>
谢谢最可爱的贝玺
</div>















