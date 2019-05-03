---
title: 【SSR】nuxt.js服务端渲染体验笔记
date: 2019-05-02 18:16:27
categories: [SSR, SEO, Vue]
tags: SEO
---


> 官网: https://zh.nuxtjs.org/


这段时间公司招了个专门搞seo优化的,  经过他的建议我们需要整改一系列之前没有注意的细节来提升SEO的优化

给我们提出了大致以下的建议
1. 对非必要跟踪的页面的`a`标签添加 `noffollow`属性
2. 需要对首页的`meta`的`title`,` keyword`, `description`进行设置, 而且要避免大写
3. 网页静态化, 使用 `/`路由参数, 而不是`query`形式的参数(`?`)
4. 去掉无关页面的`keyword`和 `description`标签
5. 必须要有404页面
6. 提供一个专门给爬虫爬取的`新闻栏目`, 并且入口对用户不可见
7. 生成`sitemap.html`和`sitemap.xml`文件,  并在网站底部"链接"的地方以"网站地图"和"XML"添加
8. 配置`robots.txt`文件
9. 需要SSR


最后一点也就是工程量最大的一点--SSR, 就是使用`Nuxt.js`的目的

怎么起步就不说了, 官网写的很好, 这里就记录一下本次尝试中花了比较多的时间去理解的地方, 以及坑

## compoents 和 pages目录
顾名思义, Pages 文件夹应该存放的是页面, components应该是组件,  这其实在最开始是比较容易混淆的,  在我们平时习惯性的使用vue的时候, 一个vue文件既可以作为组件也可以作为页面,  但在 `Nuxt` 中是类似但不完全相同,  

pages 中的页面组件具有被强化的几个方法
![页面组件](http://img.nixiaolei.com/2019-05-02-19-08-21.png)

而components 则确实是组件的定义,  它就是最普通的`vue`组件

## 可选与必选动态路由

Nuxt.js 的路由按照pages 文件夹的具体目录结构自动划分,  动态路由也是如此, 但动态路由可被分为可选和必选,  

可选路由, 访问路径demo: `people/123`
```
pages/
--| people/
-----| _id.vue
--| _.vue
--| index.vue
```



这样的结构是 必选路由,  访问路径demo: `people/123/456`
> 同时可接收 123 和 456 两个参数, 并且 123 参数必须有
```
pages/
--| people/
-----| _id/
---------| _id.vue
---------| index.vue
--| _.vue
--| index.vue
```



## 404页面
`_.vue` 可以用来充当路由未被匹配上的情况, 所以我们需要的404页面就可以使用`_.vue`来编写, 放在pages目录下


```
pages/
--| _.vue
--| index.vue
```

## 使用axios
必须要在`nuxt.config.js`中添加关于`axios`的配置, 才能使用`this.$axios`访问到
```Js
axios: {
  proxy: true,
  prefix: 'http://jsonplaceholder.typicode.com', // baseURL
  credentials: true,
},
proxy: {
  '/api/': {
    target: 'http://127.0.0.1:2001', // 代理地址
    changeOrigin: true,
    pathRewrite: {
      '^/api': ''
    },
  },
},
```

## 使用axios
不需要创建 `vuex`实例
只需要把你的 `state`,`getters`等等导出即可,  命名空间的方式也是如此,只需要多建一个文件即可

如下:
`store/index.js`
```Js
export const state = () => ({
  userInfo: 0
})

export const getters = {
  getUserInfo (state) {
    return state.userInfo
  }
}
export const mutations = {
  setUserInfo (state, data) {
    state.userInfo = data
  }
}
```

`fetch.js`
```Js
export const state = () => ({
  posts: null
})

export const getters = {
  getPosts (state) {
    return state.posts
  }
}
export const mutations = {
  setPosts (state, data) {
    state.posts = data
  }
}
```


使用`fetch`中的state时:
```Js
  // 写数据
  this.$store.commit('fetch/setPosts', res.data)

  // 取数据
  this.posts = this.$store.getters['fetch/getPosts'] 
```



## 服务端与客户端数据传递
利用vuex里提供的`nuxtServerInit`,  Nuxt.js 调用它的时候会将页面的上下文对象作为第2个参数传给它（服务端调用时才会酱紫哟）

举个例子，假设我们服务端的会话状态树里可以通过 req.session.user 来访问当前登录的用户。将该登录用户信息传给客户端的状态树，我们只需更新 store/index.js 如下：
```Js
actions: {
  nuxtServerInit ({ commit }, { req }) {
    if (req.session.user) {
      commit('user', req.session.user)
    }
  }
}
```
> 如果你使用_状态树模块化_的模式，只有主模块（即 store/index.js）适用设置该方法（其他模块设置了也不会被调用）。

## 生命周期的调用环境
在任何 Vue 组件的生命周期内， 只有 beforeCreate 和 created 这两个方法会在 客户端和服务端被调用。其他生命周期函数仅在客户端被调用。




