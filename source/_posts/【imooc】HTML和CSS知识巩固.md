---
title: HTML和CSS巩固
date: 2019-04-10 22:38:41
categories: HTML
tags: [HTML, css]
---




# HTML
> 如何理解HTML
> html "文档"
> 描述文档的"结构"
> 有区块和大纲
* `<base href="/">` 指定基础路径 ,只有所有的链接都是以该路径为基准来进行计算
* `label` 中 ` for` = 表单项的`id` 可以和表单项关联, 你点击这个`label`就好比点击这个表单项本身 
* `button tyle="reset">`  初始化表单
*  jQurey 的  `$("form").serialize()`  的方法可以批量获得表单的属性
*  了解下html 5的 `内容模型` 分类规则 `https://www.w3.org/TR/html5/text-level-semantics.html#the-a-element`
* 块级元素不一定能包含块级元素  如: p 中不能包含div  ,强行包裹渲染时浏览器会进行容错模式会以为你写错了, p的开始标签会被当成一个标签,div 还是div ,p 的结束标签又会被当成一个独立的标签
* 行内元素一般不能包含块级元素 ,  但也有例外, 比如:  a  但在html4中是不合法的,,h5中计算时会将其透明化,也就是说计算合法性时会不考虑 a , 但比如  a>div 这样包裹,还是要取决于 它们外部时什么元素才能决定是否合法
* a 中同样不能包含  a  强行包裹渲染时会被分离

## html 元素默认样式
> 默认样式的意义在于, 你可以直接书写代码就会在页面上出现效果,而不是每创建一个元素都要去临时给他 宽高颜色之类的 才会在页面上出现\
> 但也会带来一些问题, 有时会导致和我预期想要实现效果截然不同的情况
> 所以需要适当清除默认样式  叫做 `css Reset`  可以去百度下著名的写法,比如 : 雅虎 YUI  的 css reset 写法 或者是 `Normalize.css` 让默认样式有效化

## 面试题
* doctype的意义是什么
  * 让浏览器以标准模式渲染
  * 让浏览器知道元素的合法性
* html、xhtml、html5之间的关系
  * HTML属于SGML
  * XHTML 属于XML, 是XTML进行xml严格化的结果
  * HTML5不属于SGML或XML,比XHTML宽松
* HTML 5 有什么变化
  * 新的语义化元素
  * 表单增强
  * 新的API (离线、音视频、实时通信、本地存储、设备能力  )
  * 分类和嵌套变更
* em 和 i 有什么区别
  * em是语义化的标签, 表强调
  * i 是纯样式的标签, 表示斜体
  * HTML5中 i 不推荐使用, 一般用作图标
* 语义化的意义是什么
  * 开发者容易理解
  * 机器容易理解结构  (搜索、读屏软件)   ,比如 大纲算法
  * 有助于 SEO 
  * semantic microdata  增强语义化   , 详细的需要去百度
* 哪些元素可以自闭和 ( 就是在该标签内部不能再带其他东西了 )
  * 表单元素 Input
  * 图片 img
  * br hr
  * meta link
* HTML 和 DOM 的关系
  * HTML是 " 死 " 的
  * DOM 由HTML解析而来, 是活的
  * js 可以维护DOM
* property 和 attribute 的区别
  * attribute 是 "死" 的  , 是你写在HTML上的属性
  * property  是 " 活 " 的 , 是dom 解析后获得的
* form的作用有哪些
  * 直接提交表单
  * 使用submit / reset 按钮
  * 便于浏览器保存表单
  * 第三库可以整体提取值 如: JQuery
  * 第三方库可以进行表单验证, 比如 angular



# css

## 基础
> 全称  Cascading Style Sheet 层叠样式表
* id选择器 样式的权重优先级最高 class选择器 其次
* 解析方式, `body div a { }` 这样的一个css 浏览器是先找到a标签,再找到它父级有没有 div ,再去找 body  ,总结就是: 从右往左解析,目的就是加快 浏览器对css 的解析速度,更快确定是哪些元素
* 伪类和伪元素的区别

## 选择器
### 选择器分类
* 元素选择器 a{}
* 伪元素选择器 ::before{}
* 类选择器 .link{}
* 属性选择器 [type = radio] {}
* 伪类选择器 :hover{}
* id选择器 #id{}
* 组合选择器 [type=checkbox] + label{}
* 否定选择器 :not(.link){}
* 通用选择器 * {}
### 选择器权重
> 必须遵守的是  只要有id 选择器 永远比 class 选择器要大, 并不能通过选择器数量盖过 id 选择器的权重,  官大一级压死人  , class再多  权重 也无法 从 十位 进到 百位, 因为权重不进位
>

* ID 选择器 #id{}  +100
* 类  属性  伪类 选择器  +10
* 元素 伪元素   +1
* 其他选择器   +0
* 还有一些需要注意的
  * !important 优先级最高 ( 不管你出现在哪,不管是谁写的 ,永远最高 )
  * 元素属性 优先级高  ==> 比ID 选择器还要高  ( 元素属性上写style 比外联和内嵌样式表优先级高 )
  * 相同权重 后写的生效

## 行高
* line-hight 会撑起 block 元素, inline 则本身无变化,但实际占用高度会被影响
* 图片下方会出现空隙的原因 : 因为图片是 按inline 来排版, 参照的是基线 ( base-line ), 而基线和底线又有一定的距离,这个距离视字体大小而定 , 例如当12px时 ,这个距离大约是3px 

## css  Hack
> 在一部分浏览器上生效的写法
* Hack 即不合法但生效的写法
* 主要用于区分不同浏览器
* 缺点: 难理解  难维护 易失效
* 替代方案: 特性检测
* 替代方案: 针对性加 class

## 取色方式
* RGB 方式 (方便,但是人眼对rgb不是很敏感)
    * #fff
    * rgb
    * rgba
* hsl ( 色相, 饱和度,  亮度) 
    * hsl 
    * hsla( 色相, 饱和度,  亮度, 透明度) 
## 字体
### 载入自定义字体
```css
@font-face{
    font-family:"IF";
    src: url("./IndieFlower.ttf")
}
.test{
    font-family: IF;
}

```
## 面试题
* css 样式 ( 选择器 ) 的优先级
  * 计算权重确定
  * !important
  * 内联样式
  * 后写的优先级高
* 雪碧图的作用
  * 减少http请求次数 提高加载性能
  * 有些情况下可以减少图片大小
* 自定义字体的使用场景
  * 1.宣传 / 品牌 / Banner等固定文案
  * 2.字体图标
* base64 的使用
  * 减少http请求
  * 适用于小图片
  * base64的体积约为原图 4 / 3
* 伪类和伪元素的区别
  * 伪类表状态
  * 伪元素是真的有元素
  * 前者单冒号, 后者双冒号
* 如何梅花checkbox
  * label [ for ] 和 id
  * 隐藏原生 Input
  * :checked + label

# postcss 
> npm install  postcss  -g
> 文件输出命令 
> postcss  src/aa.css   -o  bb.css 

## autoprefixer 自动加前缀
> npm install  autoprefixer

## psostcss-import 模块化css
> npm install  postcss-import

## cssnano  压缩css代码
> npm install  cssnano

##  cssnext    使用浏览器未支持的css
> npm install  postcss-cssnext

## precss  用预处理的语法写css /循环/变量
```
const autoprefixer = require('autoprefixer')
const atImport = require('postcss-import')
module.exports = {
    plugins: [
        atImport,
        autoprefixer(
            {
            browsers:['>0%']
        }
    )
    ]
}
```

# webpack
## webpack 打包后js 与 css 分离
> npm install extract-text-webpack-plugin





> ![1528628764809](C:\Users\Lei\AppData\Local\Temp\1528628764809.png)

* loader 配置

![1528628596610](C:\Users\Lei\AppData\Local\Temp\1528628596610.png)



* 文件输出

  ![1528628686140](C:\Users\Lei\AppData\Local\Temp\1528628686140.png)





# css布局

## 面试题

* 实现两栏 ( 三栏 ) 布局
  * 表格布局
  * float + margin 布局
  * inline-block布局
  * flexbox布局
* position:absolute / fixed 有什么区别
  * 前者相对最近的absolute / relative 进行定位
  * 后者相对屏幕 或 移动端的 viewport 进行定位
* display:inline-block 的间隙
  * 原因: 字符间距
  * 解决: 消灭字符或者消灭间距
* 如何清楚浮动
  * 让盒子负责自己的布局
  * overflow:hidden( auto )
  * ::after{ clear:both }
* 如何适配移动端页面
  * viewport
  * rem / viewport / media query
  * 设计上: 隐藏  折行  自适应



# 浏览器小技巧

* `$0` 获取当前选中的元素DOM












