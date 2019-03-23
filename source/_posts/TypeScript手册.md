---
title: TypeScript手册
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



## 泛型

什么是泛型?

泛型: 把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型

一句话理解就是：  参数化类型（把类型当作是参数一样传递）

使用泛型的好处:
* 代码更加简洁【不用强制转换】
* 程序更加健壮【只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常】
* 可读性和稳定性【在编写集合的时候，就限定了类型】


指定一个最简单的泛型
```TypeScript
function Hello<T>(arg:T):T {
  return arg;
}
```




## Module 模块

Module 模式是常见的JavaScript模式之一， 在ES6的模块话实现之前， 人们通常通过 IFEE 来实现Module

Module 的好处
1. 模块化
2. 可重用
3. 封装变量和函数



来看看在ES6之前是如何书写的
```JavaScript
(function() {
  // do somtiong
}());
```


让我们来看一个例子， 使用 TS 的 Module 来防止调用多种 interface 时的麻烦

使用Module之前
```TypeScript
interface StringValidator {
  // 接口定义的方法
  isAcceptable(s:string):boolean;
}
var lettersRegexp = /^[A-Za-z]+$]/;
var numberRegexp = /^[0-9]+$/;
// 实现接口的方法
class LettersOnlyValidator implements StringValidator {
  isAcceptable(s:string):boolean {
    return lettersRegexp.test(s)
  }
} 
class ZipCodeValidator implements StringValidator {
  isAcceptable(s:string):boolean {
    return s.length === 5 && numberRegexp.test(s)
  }
}
```

使用了Module 的书写方式后

```TypeScript
module Validation {
  // 需要使用 export 来表示对外暴露
  export interface StringValidator {
    isAcceptable(s:string): boolean;
  }

  var lettersRegexp = /^[A-Za-z]+$]/;
  var numberRegexp = /^[0-9]+$/;

  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s:string) {
      return lettersRegexp.test(s)
    }
  }
  export class ZipCodeValidator implements StringValidator {
    isAcceptable(s:string):boolean {
      return s.length === 5 && numberRegexp.test(s)
    }
  }
}
```


Module 与 类相结合使用
```TypeScript
module Person {
  export class Student {
    name: string;
    constructor(name:string) {
      this.name = name;
    }
    study() {
      console.log(this.name + '在学习')
    }
    speak() {
      console.log(this.name + '在说话')
    }
  }
}

// ----------- 使用时 -------------

const xiaoming = new Person.Student('小明')
xiaoming.study()

```



## Namespaces

Namespaces 的和Module 的用法相同，  在Ts 的1.5 版本后， 用 Namespaces 取代了 Module 

查看一下官方的示例

```TypeScript
namespace Shapes {
    export namespace Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as 'new Shapes.Polygons.Square()'
```


## 装饰器 < Decorators >

如何定义一个装饰器

其实装饰器就是一个普通的函数， 他接收一个参数`target` ，这个参数就是被装饰对象， 看下面的例子， `sealed` 装饰了`Dog`类， 并且在`sealed` 中打印了 `target` ， 让我们看看 `target`到底是什么？
```TypeScript
function sealed(target:any) {
  // do something with 'target' ...
  console.log(target)
  return target
}


// 被装饰的对象 foo
@sealed
class Dog {

}
```

打印结果
![使用装饰器](http://img.nixiaolei.com/2019-03-22-22-06-26.png)

我们可以看到， 得到的正是 `Dog`这个类



### 装饰器工厂< Decorator Factories >

> 如果希望自定义如何将装饰器应用于声明，可以编写装饰器工厂。装饰器工厂只是一个函数，它返回将在运行时由装饰器调用的表达式。


装饰器工厂和普通装饰器相比的好处就是更加的自定义了， 可以根据不同情况自定义效果


如何定义一个`装饰器工厂`

```TypeScript
function dec(value:string) {
  return function (target:any)  {
    console.log(value)
    console.log(target)    
  }
}

// 在这里使用并传入自定义的参数
@dec('test')
class Cat {
  
}
```

让我们来看看运行的结果

![定义装饰器工厂](http://img.nixiaolei.com/2019-03-22-22-22-55.png)

可以看到，我们传入的`test`字符串和 `Cat`类都被打印出来了，

### 多装饰器的执行顺序
使用多个装饰器时如何书写呢?

当我们使用多个装饰器时有两种写法

1. 写成一行
```TypeScript
@f @g x
```
2. 垂直书写
```TypeScript
@f
@g
x
```
**多个装饰器的执行顺序是如何的呢？**

套用官网的说明
1. 对每个装饰器的表达式进行自上而下的计算。
2. 然后将结果作为自下而上的函数调用。


其实真实的运行原理是
```TypeScript
f(g(x))
```

### 访问器修饰器

访问器修饰器的接收的参数和方法修饰器接收的参数一样


```TypeScript
function dec(value:string) {
  return function (target:any, propertyKey: string, descriptor: PropertyDescriptor)  {
  }
}

// 在这里使用并传入自定义的参数
@dec('test')
class Cat {
  private _x: number;
  private _y: number;

  constructor(x: number, y: number) {
    this._x = x
    this._y = y
  }
  // 访问器修饰器
  @dec(true)
  get  x() { return this._x }
  @dec(false)
  get  y() { return this._y }
}
```


### 属性修饰器

属性修饰器只有前两个参数
```TypeScript
function dec(value:string) {
  return function (target:any, propertyKey: string)  {
  }
}

// 在这里使用并传入自定义的参数
@dec('test')
class Cat {
  private _x: number;
  private _y: number;

  constructor(x: number, y: number) {
    // 属性修饰器
    @dec(false)
    this._x = x
    this._y = y
  }
}
```

### 参数修饰器
参数修饰器也是三个参数， 但不同的是第三个参数接收

**参数装饰器只能确认一个方法的参数是否被传入**


前两个和其他的修饰器都相同， 

1. target 对于静态成员是类的构造函数， 而对于实例成员是类的实例对象
2. propertyKey 成员的名称
3. 参数在所处函数的参数列表中的索引， 说人话就是告诉你他是第几个参数



```TypeScript
function require(value:boolean) {
  return function (target:any, propertyKey: string, index: number)  {
  }
}

// 在这里使用并传入自定义的参数
@dec('test')
class Cat {
  run(@require(true) name:string) {
  }
}
```


### 装饰器参数

1. 类装饰器

类装饰器的参数只接收一个， 那就是`target` 也就是这个类本身

就和上面演示的那些例子一样


2. 方法装饰器

一般会传入三个参数

1. target 对于静态成员是类的构造函数， 而对于实例成员是类的实例对象
2. propertyKey 成员的名称
3. descriptor 成员属性的描述符


我们来验证下方法装饰器的参数

让我们看看这段例子会输出什么
```TypeScript
function f(value:string) {
  return function (target:any, propertyKey: string, descriptor: PropertyDescriptor)  {
    console.log(arguments)
    console.log(target)    
  }
}

class Tiger {
  @f('tiger run')
  run() {
  }
}
```
输入结果: 

![方法装饰器参数](http://img.nixiaolei.com/2019-03-23-11-35-28.png)


在控制台输出， 我们看到了， 
第一个参数是 `tiger` 的实例对象（因为`run`是实例成员）

第二个参数是成员名称， 也就是被装饰的`run`

第三个参数是 `run` 的属性描述符


## Mixins 混入
除了传统的面向对象继承方式，还流行一种通过可重用组件创建类的方式，就是联合另一个简单类的代码。 你可能在Scala等语言里对mixins及其特性已经很熟悉了，但它在JavaScript中也是很流行的。


Mixins 其实可以拆分为两个单词

Mix : 混合

ins : 是进入

所以合在一起就是叫 `混入` 的意思的单词

它想要表达的是一个对象中混入另一个对象的方法


**让我们来实现一下Mixins**

代码里首先定义两个类，它们将做为mixins。 可以看到每个类都只定义了一个特定的行为或功能。 稍后我们使用它们来创建一个新类，同时具有这两种功能。

```TypeScript
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }

}

// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}
```
创建一个类，结合了这两个mixins。 下面来看一下具体是怎么操作的：
```TypeScript

class SmartObject implements Disposable, Activatable { }
```
这里我们没有使用extends而是使用implements。 把类当成了接口，仅使用Disposable和Activatable的类型而非其实现。 这意味着我们需要在类里面实现接口。 但是这是我们在用mixin时想避免的。

我们可以这么做来达到目的，为将要mixin进来的属性方法创建出占位属性。 这告诉编译器这些成员在运行时是可用的。 这样就能使用mixin带来的便利，虽说需要提前定义一些占位属性。

也就是这样

```TypeScript
class SmartObject implements Disposable, Activatable {
  constructor() {
      setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
  }

  interact() {
      this.activate();
  }

  // Disposable
  isDisposed: boolean = false;
  dispose: () => void;
  // Activatable
  isActive: boolean = false;
  activate: () => void;
  deactivate: () => void;
}
```

最后，把mixins混入定义的类，完成全部实现部分。
```TypeScript
applyMixins(SmartObject, [Disposable, Activatable]);
```

我们同时也需要创建这个帮助函数， 帮我们做混入操作。 它会遍历mixins上的所有属性，并复制到目标上去，把之前的占位属性替换成真正的实现代码。
```TypeScript
function applyMixins(derivedCtor: any, baseCtors: any[]) {
  baseCtors.forEach(baseCtor => {
      Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
          derivedCtor.prototype[name] = baseCtor.prototype[name];
      })
  });
}
```







## 参考文献
* https://segmentfault.com/a/1190000016305647


-------------------------待完善——----------------------







