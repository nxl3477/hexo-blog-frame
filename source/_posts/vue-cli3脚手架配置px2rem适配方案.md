---
title: vue-cli3脚手架配置px2rem适配方案
date: 2019-03-24 14:26:51
categories: Vue
tags: [适配, Vue]
---

## 安装
```
npm i lib-flexible --save
npm install px2rem-loader
```

## 引入
> main.js

```
import 'lib-flexible/flexible'
```
> index.html

```
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```


## 根目录新建 vue.config.js 文件
> 复制如下代码， 重启， 搞定

```
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('css')
        .test(/\.css$/)
        .oneOf('vue')
        .resourceQuery(/\?vue/)
        .use('px2rem')
          .loader('px2rem-loader')
          .options({
            remUnit: 75
          })
  }
}
```



<div style='display: none;'>
谢谢最可爱的贝玺
</div>















