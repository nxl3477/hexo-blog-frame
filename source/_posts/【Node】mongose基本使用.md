---
title: 【Node】mongose基本使用
date: 2019-04-10 22:29:46
categories: NodeJs
tags: [NodeJs, MongoDB]
---




# NodeJS 与 MongooDB

> 在NodeJs 中推荐使用 Mongoose来对mongoDB 进行操作
> mongoose 官网  http://mongoosejs.com/


## mongoDB 的特点
*  JSON 格式的数据存储
*  非关系型数据库
*  不需要和 MYSQL 之类的关系型数据库一样简表, 使用时若没有该集合会自动创建
*  集合 => 文档 => 记录/数据字段
*  缺点就是数据操作太灵活, 容易混入脏数据

## 如何安装 mongoose 
```
npm install mongoose
```
## 导入Mongooes
* 在 Node 中 需要先使用 require 导入mongoose 模块
```
const mongoose = require('mongoose')
```


## 连接MongoDB
* 这里拿连接本地的mongoDB 示范
```
mongoose.connect('mongod://localhost/test')
```

## 引入数据库模型骨架 Schema
* Schema 在导入的 mongoose 对象中, 建议先将其的引用存入一个变量
* Schema  是一种以文件形式存储的数据库模型骨架,
* 它不具备对数据库的操作能力
* 它可以弥补 mongoDB 对数据格式控制的不足
```
const Schema = mongoose.Schema  
```
## 实例化 Schema
* 既然是模型骨架,  那我们就需要 将它的骨架搭建起来
* 以下举例 一个用户注册使用的 骨架模型
* type (数据类型)   如: String ,Number
* required (  是否允许为空 )
```
let userSchema = new Schema({
    name:{
        type: String,
        required: true
    },
    email: {
        type: String
    },
    pwd:{
        type: String,
        required: true
    }
})
```
## 发布模型 Model 
* 由搭建好的模型骨架 Schema 发布生成的模型, 具有抽象属性和数据库操作的能力
* Model 第一个参数 单数形式开头大写 , Mongoose 会自动将其转换为小写的负数形式作为文档名称
* 如: User => users
* 接下来你就可以对 User 为所欲为了, CURD
```
const User = mongoose.model('Use',userSchema) 
```
##  Sava 方法 保存数据
```
User.save()
	.then(() => console.log('保存成功'));
```

## Remove 删除数据
```
User.remove({ 删除条件 })
.then(res=>{ })
.catch(err=>{ })
```
## FindOne 返回匹配到的第一条数据
* 如果用 Find 查找是返回的数组, FindOne 返回的就是个单独的对象
* 注意返回的 的查询结果可以经过修改之后为所欲为
```
User.findOne( { 查询条件 } )
  .then(data=>{
     //you can
    // data.save()
  })
```
## Find 查找满足条件的所有
* 和 FinOne 不同的是 无论结果有多少条,它返回的始终是一个数组
```
User.find( { 查询条件 } )
  .then(data=>{
     //you can
    // data.save()
  })
```












