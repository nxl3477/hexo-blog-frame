---
title: 原生实现一个ajax
date: 2019-04-10 22:42:08
categories: JavaScript
tags: JavaScript
---

# 原生 AJAX

## 什么是AJAX

* Asynchronous  JavaScript & XML    请求XML的异步技术
* web开发的一种技术
* 异步发送 & 请求数据
* 不需要重新刷新当前页面
* 目前 JSON 数据格式以及占据市场

## 请求码 和 状态码
### readyState 请求码
* 0 : 请求未初始化  ( 没有 XHR 对象 )
* 1 : 服务器连接已建立
* 2 : 请求已接受
* 3 : 请求处理中
* 4 : 请求已完成, 且相应已就绪

### HTTP 状态码
* 200 - 服务器成功返回网页
* 300 - 请求地址错误,重定向
* 400 - 请求错误
  * 404 - 请求的网页不存在
* 500 - 服务端错误
  * 503 - 服务器暂时不可用    

## 创建  XMLHttpRequest 对象
```js
var xhr = new XMLHttpRequest()
```


##  open() 请求部署阶段
* xhr.open( type 请求类型 , url/filename 路径或文件名 ,  async是否要使用异步)
* GET 请求 参数写在 URL 中
```js
 xhr.open('GET', 'http://jsonplaceholder.typicode.com/users', true)
```
## 两种方式请求 onload 和 onreadystatechange
* 请求成功后会返回服务器 返还的相应内容 `responseText`

### onload
* 只有请求码为 4 时才会进入 onload
```js
xhr.onload = function(){
  console.log(this.responseText)
}
```

###  onprogress 捕获 为3 的请求码
```js
xhr.onprogress =function(){
  console.log(this.readyState)
}
```

### onreadystatechange
* 需要判断返回的请求码 , 直接输出会返回两次数据 
```js
xhr.onreadystatechange = function(){
  console.log(this.responseText)
}
```
####  readyState

* 返回请求码的位置与顺序
```js
 var xhr = new XMLHttpRequest()
 console.log(xhr.readyState) // 0
 xhr.open('GET', 'http://jsonplaceholder.typicode.com/users', true)
 console.log(xhr.readyState)  // 1
 xhr.onreadystatechange = function(){
  console.log('readyState:',xhr.readyState)  // 2 , 3 , 4
 }
 xhr.send()
```
####  status 状态码
* 每次都和请求码一同被返回

#### 根据 请求码 和 状态码 来操作数据

#### 一个完整的 GET 请求
```js
 var xhr = new XMLHttpRequest()
  xhr.open('GET', 'http://jsonplaceholder.typicode.com/users', true)
  xhr.onreadystatechange = function(){
      if(this.status == 200 && this.readyState == 4){
          console.log(this.responseText)
      }
  }
  xhr.send()
```
## 发送请求
* GET 直接发送
* POST 需要带上要发送的 请求体 数据
```js
xhr.send()
```












