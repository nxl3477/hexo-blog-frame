---
title: 【canvas】如何处理Retina屏模糊问题
date: 2019-05-21 22:28:40
categories: Canvas
tags: Canvas
---


## 什么是Retina屏？
所谓“Retina”是一种显示标准，是把更多的像素点压缩至一块屏幕里，从而达到更高的分辨率并提高屏幕显示的细腻程度。由摩托罗拉公司研发。最初该技术是用于Moto Aura上。这种分辨率在正常观看距离下足以使人肉眼无法分辨其中的单独像素。也被称为视网膜显示屏。


## canvas在Retina 屏幕模糊怎么办
> DPI（Dots Per Inch，每英寸点数）是一个量度单位，用于点阵数码影像，指每一英寸长度中，取样、可显示或输出点的数目。

利用获取设备的dpi, 将canvas 画布扩大至dpi的倍数， 然后将其缩小， 就可以得到清晰的canvas 效果









