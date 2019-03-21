---
title: hexo中如何使用本地图片
date: 2019-03-16 11:20:19
categories: hexo
tags: hexo
---
<script type="text/javascript" src="/js/src/bai.js"></script>


首先需要修改`_config.yml`配置文件`post_asset_folder`项为`true`

使用命令创建博客
```
hexo new 你的文章名称
```

他会生成`.md`文件及一个对应的文件夹， 我们将图片放到该文件夹中



## 方法一

在文章中这样插入图片
```
{% asset_img 这是一个新的博客的图片.jpg 这是一个新的博客的图片的说明 %}
```

效果如下:

![皮卡皮卡](http://img.nixiaolei.com/pika.png)





## 方法二
> 这个方法在首页无法正常预览图片


安装一下这个插件

```shell
npm install hexo-asset-image --save
```


在文章中这样插入图片
```
![这是一张图片](xxxx.png)
```


效果如下:

![皮卡皮卡](http://img.nixiaolei.com/pika.png)



参考文献
> http://etrd.org/2017/01/23/hexo%E4%B8%AD%E5%AE%8C%E7%BE%8E%E6%8F%92%E5%85%A5%E6%9C%AC%E5%9C%B0%E5%9B%BE%E7%89%87/