---
title: 【imooc】HTML5&CSS3
date: 2019-04-10 22:40:47
categories: HTML
tags: [HTML, css]
---




# HTML5 + CSS3

## 语义化标签

| 标签名 |   作用   |
| :----: | :------: |
|  nav   | 导航 |
|header|页眉|
|footer|文档或者页的页脚|
|main|主要内容|
|article|文章或热点区|
|aside|主题内容之外|

## 语义化标签的兼容性



* IE9: 行级元素在设置宽度的时候失效, 解决方法: display : block
* IE8: 完全不支持语义标签( 不支持html5 )  ,解决方法: `document.createElement('header')` 或引入第三方插件 ` html5shiv`



## form 新增的属性

* autofocus 浏览器打开自动获得焦点
* autocomplete="on"   表单内容自动完成,前提: 必须成功提交过  , 该标签必须有name 属性
* required 必须输入
* pattern=" "    正则表达式验证
* multiple  对于file 标签使用,  同时选择多个文件, 对于email 标签使用, 可以同时输入多个邮箱
* form="表单form 的 id "  可以让在form 表单外部的 表单数据也参与提交
* oninvalid 当验证不通过时触发的事件
* this.setCustomValidity(" 请输入合法的手机号 ")     这个js api 可以修改默认的h5表单 提示内容
* `dom.dataset[ "myTest" ]`  获取自定义的data-my-Test的值 ,获取时要变成驼峰命名

## form 新增的元素
* datalist   可以输入的下拉列表 (   firfox 中不支持 )   需要通过  list="datalist的id" 建立联系
	* 	如果 input 的type 类型是url ,那么value值必须添加 http://  ,因为不加的话识别为不合法
```
    <!-- 建立关联   list="datalist的id" -->
    <input type="text" list="dt">
    <datalist id="dt">
        <option value="哈哈" label="10人">哈哈哈</option>
        <option value="前端牛逼" label="18人">前端牛逼</option>
    </datalist>
```

*  keygen标签       h5 提供的加密  , 部分浏览器不支持 , 将表单信息进的是非对称加密,
  * 生成公钥和私钥,   提交数据的时候  信息+私钥 => 二次加密   然后发送给服务器,  服务器用接收到的 公钥进行解密数据
*  output  显示输出信息, 只能显示不能修改
  * 语义性更强
  * 值需要你去设置, 不能自动计算

## 多媒体标签
* embed  直接插入视频文件， 它的本质是调用本机上已经安装的软件  有兼容性

* flash插件   安装flash   1.学习flash,增加使用成本    2.苹果设备不支持flash

* audio 音频 

  *   src : 播放音频文件的路径
  * controls: 音频播放器的控制面板
  *  autoplay: 自动播放
  * loop: 循环

* video 视频 
  *   src : 播放音频文件的路径

  * controls: 音频播放器的控制面板

  *  autoplay: 自动播放

  * loop: 循环

  *  width:宽度 

  * height:高度

  *  poster: 当视频还没完全下载，  或者用户还没点击播放前的默认显示的封面， 默认是显示当前视频文件的第一帧

  * 设置宽高的时候，一般情况下只会设置宽度或者高度 让其自动等比缩放， 如果同时设置宽度和高度，那么视频不会真正的调整到设置的高度，除非设置刚好等比例

  * 多个source 让浏览器自动选择支持的视频格式 先看第一个支不支持再看第二个 

  * ```
     <video  autoplay controls width="600px">
            <!-- 多个source 让浏览器自动选择支持的视频格式 先看第一个支不支持再看第二个 -->
            <source src="http://www.baidu.com" type="video/mp4">
            <source src="http://wxhboy.com/login/movies/w.mp4" type="video/mp4">
                您的浏览器不支持当前的视频播放
        </video>
     ```



## 操作class的API

* `dom.classList.add('bg')` 增加class
* `obj.classList.remove('bg')`  删除class
* `obj.classList.toggle('bg')` 切换class  如果存在就删除,如果不存在就增加该class
* `obj.classList.contains('bg')`  判断是否存在某个class  , 返回布尔型 



## H5新增的接口 API

* ononline  网络连通时触发这个事件 ，只在window上 
* onoffline  网络断开时触发事件
* `element.requestFullscreen` 开启全屏 ，需要加浏览器前缀
  *  需要加兼容浏览器的前缀 chrome:webkit   firefox:moz  ie: ms  opera: o 
* `document.cancelFullScreen` 取消全屏，只能是document对象

  *  需要加兼容浏览器的前缀 chrome:webkit   firefox:moz  ie: ms  opera: o 
* `document.fullscreenElement` 取消全屏，只能是document对象

   - 需要加兼容浏览器的前缀 chrome:webkit   firefox:moz  ie: ms  opera: o 
* ` var reader = new FileReader()   reader.readAsDataURL(file[0])   `读取文件斌转为base64
  * 没有返回值,但是它会将读取的结果存储在文件读取对象的result中 
  * FileReader 提供一个完整的事件模型, 用来捕获读取文件时的状态
  * onabort: 读取文件中断片时触发
  * onerror: 读取文件错误时触发
  * onload: 文件读取成功完成时触发
           *  onloadend: 读取完成时触发,无论成功还是失败
         * onloadstart: 开始读取文件时触发
         * onprogress: 读取文件过程中持续触发

# CSS3 选择器

## 属性选择器
> 属性时相对于标签而言的
> 就是根据指定名称的属性的值来查找元素

*  `li[style]` 查找只要拥有 style 属性的标签
*  `li[class = red ]`  严格匹配  拥有class 样式并且等于某值
*  `li[class*= red ]`  查找class 中包含 red 的标签
*  `li[class^= red ]`  查找class 中以red 开头的li标签
*  `li[class$= red ]`  查找class 中以red 结尾的li标签

## 伪类选择器
### 兄弟伪类
* `+` 获取当前元素相邻的满足条件的元素，必须是相邻的
* `~` 获取当前元素的满足条件的兄弟元素  可以不相邻 ，所有兄弟元素

### 相对于父级选择器
* `ul> li:first-child` 查找ul下第一个元素，如果第一个不是li 那就不生效
* `ul> li:last-child`  查找ul下最后一个元素
* `li:first-of-type`   查找的时候指挥查找满足类型条件的第一个元素，过滤掉其他类型的元素
* `li:last-of-type`   查找的时候指挥查找满足类型条件的最后一个元素
* `li:nth-child()` 指定索引位置 nth-child(从1开始的所以||关键字||表达式) /  该方法同样 > 如果第一个不是li 那就不生效
  * even 偶数
  * odd  奇数
* ` li:nth-of-type(even) `    指定类型的 所有偶数 li 
* `li:nth-of-type(-n+5) `    只选择前5个li
* `li:nth-last-of-type(-n+5) `    只选择最后5个li
* `li:empty `     选择内容为空的li

## 伪类样式
* ` h2:target ` 可以为锚点目标元素添加样式，当目标元素被触发为当前锚链接的目标时，调用此伪类样式

## 伪元素
> 是一个行内元素， 如果想设置宽高需要转换成block|| float || postition
> 必须添加 content, 哪怕不设置内容， 也要 content:""

* `e::before`
* `e::after`
* `e::first-letter`  文本的第一个字母或字(不是词组) 
  * 依靠float 实现首字下沉
* `e::first-line`   文本第一行  ，  如果第一个字设置了`::first-letter` 这些样式都对它无效
* `e::selection`  可改变选中文本的样式，只能设置显示的样式，而不能设置内容的大小

## 渐变

>  shape : 渐变的形状
>
> ellipse 表示椭圆形（适配当前的形状），circle 表示正圆形 
>
> 如果宽 高不一样  默认效果切换到 ellipse



## 多列布局
> 固定列数时可以使用
> 有点类似表格布局
> 比较适合文字

* 只需再父容器上 加上 `column-count: 3`  想要几列就写几
* `  column-rule:dashed 3px red;` 设置边框样式，这是虚线边框
* ` column-gap: 0px;`   设置列与列之间的间距 , 为0时是没有边框的
* ` column-width: 500px;`  
  *  取大优先，看看是自动计算的宽度大还是你设置的大

    * 但是会填充整个屏幕 ，意味最终的宽度可能会大于设置的宽度--

    ​        填充满整个屏幕

    * 如果人为设置宽度更小，使用自动计算的宽度
* `column-span: all;` 使某文字或某元素占满一整行














