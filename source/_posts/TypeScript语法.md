---
title: TypeScript语法
date: 2019-03-24 14:47:50
categories: TypeScript
tags: [ JavaScript, TypeScript ]
---



## 初始化配置文件
> tsc --init      
> 修改` outDir`

## 定义数组
> 1

```TypeScript
var arr:number[] = [1,2,3,4]
```

> 2

```TypeScript
var arr:Array<number> = [3,4,56,7,8,9]
```
> 3

```TypeScript
var arr:any[] = [1,'5515', false]
```


## 定义元组
> 元组表示一个已知元素数量和类型的数组，各元素的类型不必相同。   
> 1

```TypeScript
let arr:[number, string] = [123, 'this is ts']
```

## 定义枚举
> 用于处理非数值的数据

```TypeScript
enum Flag { success=1, error=2 };
let s:Flag=Flag.success

console.log(s) // 1
```

## never 类型: 
> never类型表示的是那些永不存在的值的类型, 如null, undefind, error

```TypeScript
var a:undefined
a = undefined
```

## 定义 symbol类型
> symbol类型的值是通过Symbol构造函数创建的。

```TypeScript
let sym1 = Symbol();
```



## 容许多个类型
```TypeScript
var num:number | undefined;

num = 123;
console.log(num)
```

## 有返回值类型
> 函数声明

```TypeScript
function run():string {
  return '32132132'
}
```

> 函数表达式

```TypeScript
var fun2 = function():number {
  return 132
}
```


## 无返回值类型
```TypeScript
function run():void{
  console.log('run')
}

```


## 可选参数、 默认参数 、剩余参数
> 默认必传, 带‘?’可传可不传， 必须配置到参数的最后面

```TypeScript
function getInfo(name:string, age?:number):string {
  return `${name} ---- ${age}`
}
```
> 默认参数       
> 写了默认参数就不再需要 '?' 标记

```TypeScript
function getInfo(name:string, age:number=250):string {
  return `${name} ---- ${age}`
}
```

> 剩余参数
```

```



## json 属性约束
```TypeScript
// json属性约束
function jsonCon(label) {
    console.log(label.labelJson);
}
jsonCon({ labelJson: '你好评' });

```


## 重载
> 因为需要遵循js ， 所以无法像java一样实现重载， 实现的只能是变体， 自己判断typeof

```TypeScript
function getInfo(name:string):string;

function getInfo(name:string, age:number):string;

function getInfo(name:string, age?:any):string {
  
  // 判断类型用 typeof
  if(age) {
    return `姓名:${name}, 年龄${age}`
  }else {
    return `姓名:${name}`
  }
}

console.log(getInfo('吴大龙'))
```

## 创建类
```TypeScript
class Person{
  name:string;  // 属性 ,前面省略了Public 关键词

  constructor(name:string){    // 构造函数  实例化类的时候 触发的方法
    this.name = name;
  }
  getName():string{
    return this.name
  }
  setName(name:string):void{
    this.name=name;
  }
} 
var aa = new Person('栗色')

console.log(aa.getName())
aa.setName('吴大龙')
console.log(aa.getName())
```
> 使用类的 get 和 set

```TypeScript
class Person{
  private _name:string;
  constructor(name:string, age:number){
    this._name = name;
  }
  get name():string{
    return this._name
  }
  set name(name:string){
    this._name = name;
  }
}
const p = new Person('吴大龙', 112)
p.name = '吴强' // 赋值
console.log(p.name)  // 取值
```




## 实现继承
> 继承自上方类

```TypeScript
class Web extends Person {
  constructor(name:string){
    super(name);
  }
}

var w=new Web('往');
console.log(w.run())
```
## 类修饰符
> 不加修饰符默认就是 Public  ,  ==外部的意思就是指不被内部函数里被调用, 而是被外部人为调用==    
> public:  共有,  在类的里面, 子类, 类外部都可以访问

> protected:  保护类型, 在类里面, 子类里面可以访问, 类外部无法访问

> private:   私有, 在类里可以访问, 子类, 类外部都没法访问

## 静态属性和静态方法
> static  静态方法, 在没有 new 出实例前都可以调用      
> ==但是没法直接调用类里面的属性==

```TypeScript
class Person {
  public nickname:string= '吴大龙';
  
  static speak():void{
    console.log(`用户姓名:${this.nickname}`)
  }
}

Person.speak()  // 用户姓名:undefined
```


## 多态
> 父类定义一个方法不去实现, 让继承它的子类去实现, 每一个子类有不同的表现       
> 也是继承的一种表现, 子类实现或重写父类中的已有的方法， 这叫多态

```TypeScript
// 多态
class Animal {
  name:string;
  constructor(name:string){
    this.name = name;
  }
  eat():void{
    console.log(`${this.name}， 在吃屎`)
  }
}

class Dog extends Animal {
  constructor(name:string){
    super(name)
  }
  // 子类重新定义 eat方法
  eat():void {
    console.log(this.name+'吃肉')
  }
}

const wuxiaohong = new Dog('吴晓红')
wuxiaohong.eat()

// 子类重新定义 eat方法
class Cat extends Animal {
  constructor(name:string) {
    super(name)
  }
  eat():void{
    console.log(this.name+ '吃粮食')
  }
}

var cc = new Cat('吴强')
cc.eat()
```

## 抽象类
> 用abstract关键字定义抽象类和抽象方法， 是提供其他类继承的基类    
> ==抽象方法只能放在抽象类里==      
> 从抽象类继承的方法必须被子类重写, 重新实现

```TypeScript
abstract class Animal{
  public name:string;
  constructor(name:string){
    this.name=name;
  }
  abstract eat():any;
}

class Dog extends Animal{

  constructor(name:any){
    super(name)
  }
  eat(){
    console.log(this.name + '吃粮食')
  }
}

var d = new Dog('小狗')
d.eat()
```

## 接口
* 也是一种定义标准. 类似抽象类, 抽象类只能规定方法， 接口可以规定属性， 函数， 索引和类

> 批量属性接口， 可定义可选参数

```TypeScript
// 属性接口
interface FullName{
  firstName: string;   //封号结束
  secondName: string;
}
function printName (name: FullName) : void{
  console.log(name.firstName +'====>' + name.secondName);
}
printName({firstName: '吴', secondName: '大龙'})  // 吴====>大龙
```
> 数组类型接口
```TypeScript
interface StringArray {
  [index:number]:string
}
var myArray:StringArray;
myArray = ["iwen", "ime"]
alert(myArray[1])
```



> 函数类型接口,  对方法传入的参数， 以及返回值进行约束

```TypeScript
// 比如建一个md5加密的接口 的接口
interface encrypt{
  (key:string, value:string):string;
}

var md5:encrypt = function(key:string, value:string):string {
  return key + value;
}
console.log(md5('name', '吴大龙'))
```



> 接口扩展， 接口可以继承接口

```TypeScript
interface Animal{
  eat():void;
}
// 继承上方接口
interface Person extends Animal {
  work():void;
}
// 有个类 实现 这个接口
class Web implements Person {
  public name:string;
  constructor(name:string){
    this.name=name
  }
  eat(){
    console.log(this.name + '喜欢吃馒头')
  }
  work(){
    console.log(this.name + '写代码')
  }
}

var w = new Web('小林')
w.work()  // 小林写代码
```
> 接口多继承  &&  ==创建一个接口对象==
```TypeScript
//  多继承 并 创建一个接口对象
interface Shape {
  color:string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength:number;
}

// 创建接口对象不需要 new  而是这样的语法
var s = <Square>{};
s.color = "blue"
s.penWidth = 10
s.sideLength = 10
```



## 泛型
* 用于创建可重用的组件， 让一个组件支持多种数据类型
* ==泛型就是解决类 接口 方法的复用性、 以及对不特定数据类型的支持==

> 基本使用 

```TypeScript
// 泛型 : 可以支持不特定的数据类型   要求： 传入的参数和返回的参数一致
function getData<T>(value:T):T{    // T表示泛型
  return value
}
console.log(getData<string>('我是泛型'))
```
> 多个泛型参数

```TypeScript
class HelloNumber<T, S>{
  Ten:T;
  add:(x:T, y:T) => S;
}
var myHelloNumber = new HelloNumber<number, string>()
myHelloNumber.Ten = 10;
myHelloNumber.add = function(x, y) {
  return x+y+''
}
console.log(myHelloNumber.add(1, 2));
```
> lamb 表达式

```TypeScript
function Hello<T>(arg:T):T{
  return arg;
}
// lamb 表达式只是表达具体 参数和返回类型, 需要重新赋值上真函数
var myHello:<K>(arg:K)=> K = <K>(arg:K):K => {
  return arg
}
alert(myHello('hello'))
```
> 另一种写法， 对象形式

```TypeScript
function Hello<T>(arg:T){
  return arg
}
var myHello:{<T>(arg:T):T} = Hello;
alert(myHello(" Hello"))
```



> 泛型接口

```TypeScript
// 泛型接口
interface ConfigFn{
  <T>(value:T):T;
}
var getData:ConfigFn = function<T>(value:T):T{
  return value
}
console.log( getData<string>('你好') )
```



> 泛型小实战 ==需要同时支持返回数字和字符串两种类型， 通过类的泛型来实现==

```TypeScript
// 泛型类: 比如有个最小堆算法， 需要同时支持返回数字和字符串两种类型， 通过类的泛型来实现

class MinClass<T>{
  list:T[]=[]
  add(value:T):void{
    this.list.push(value)
  }
  min():T{
    var minNum = this.list[0]
    for(var i=0;i<this.list.length;i++){
      if(minNum > this.list[i]){
        minNum=this.list[i]
      }
    }
    return minNum;
  }
}
var m1 = new MinClass<string>();
m1.add('1')
m1.add('you')
m1.add('love')
console.log(m1.min())
```



<div style='display: none;'>
谢谢最可爱的贝玺
</div>















