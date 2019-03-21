---
title: TypeScript入门指南
date: 2019-03-18 22:41:51
categories: TypeScript
tags: [ JavaScript, TypeScript ]
---



TypeScript是JavaScript的超集

## 开发环境搭建
> 我的环境: window10 + vscode

全局安装Ts
> npm i -g typescript

生成并配置`tsconfig.json`
> tsc --init


配置文件， 下面的配置中`ts`应存放在`src` 目录下， 并且会自动编译到dist文件夹下
```JavaScript
{
  "compilerOptions": {
    "target": "es2017",                         // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                       // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "moduleResolution": "node",                 // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "emitDecoratorMetadata": true,              // 为装饰器提供元数据的支持
    "experimentalDecorators": true,             // 启用装饰器
    "allowSyntheticDefaultImports": true,       // 允许从没有设置默认导出的模块中默认导入。
    "strict": true,                             // 启用所有严格类型检查选项
    "noImplicitAny": true,                      // 在表达式和声明上有隐含的 any类型时报错
    "alwaysStrict": true,                       // 以严格模式检查没个模块，并在没个文件里加入 'use strict'
    "sourceMap": true,
    "noEmit": false,                            // 不生成输出文件
    "removeComments": true,                     // 删除编译后的所有的注释
    "importHelpers": true,                      // 从 tslib 导入辅助工具函数
    "strictNullChecks": true,                   // 启用严格的 null 检查
    "lib": ["es2017"],                          // 指定要包含在编译中的库文件
    "typeRoots": ["node_modules/@types"],
    "types": [
      "node",
    ],
    "outDir": "./dist",                        // 编译后文件产出目录
    "rootDir": "./src"                          // 你的ts文件目录
  },
  "include": [                                 // 需要编译的ts文件一个*表示文件匹配**表示忽略文件的深度问题
    "./src/*.ts",
    "./src/**/*.ts"
  ],
  "exclude": [
    "node_modules",
    "dist",
    "**/*.test.ts",
    "public"
  ]
}
```

快速生成`package.json`
> npm init -y


运行命令， 开始监听
> tsc -w


是否有效呢？ 让我们来试一下


进入src目录新建一个 `hello.ts`，记得是`.ts`， 不要写习惯了打成`.js`😅

随便写点东西， 比如定义一个字符串类型:
```TypeScript
var a:string = "hello world"
```

如果你配置没有问题的话， 我们就会看到他监听文件修改并编译

这是ts文件与 编译后的js文件

![编译前后](http://img.nixiaolei.com/2019-03-18-22-50-51.png)


并且在他运行过程中， 我们可以按`F5`进行调试






## 基本数据类型
* Boolean
* Number
* String
* Array
* Enum
* Any
* Void


简单的几种数据的定义
```TypeScript
// 定义一个Boolean
var a:boolean = true
// 定义一个Number
var a:number = 13
// 定义一个 String 
var a:string = "13"
```

基本的数据结构定义
```TypeScript
var list:number[] = [1,2,3]


```


## 接口
> interfaces

### 基础接口

没有接口时我们如何规范参数类型
```JavaScript
function fn(dog:{name:string}) {
  console.log(dog.name)
}
var myObj = { name: "Hello" }
fn(myObj)
```

有了接口， 我们就可以把参数规定与函数分离

如何创建接口

通过 `interface` 关键字来声明

```JavaScript
interface dogValue {
  name:string;
}

// 指定参数`dog`是接口类型
function fn(dog: dogValue) {
  console.log(dog.name)
}

var myObj = { name: "Hello" }
fn(myObj)  // 编译通过
```

### 接口的可选属性
接口定义的参数不一定每一个都会被用到

![2019-03-20-22-32-38](http://img.nixiaolei.com/2019-03-20-22-32-38.png)

## 参考文献
* https://segmentfault.com/a/1190000016305647


-------------------------待完善——----------------------







