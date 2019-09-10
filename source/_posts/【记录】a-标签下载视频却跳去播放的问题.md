---
title: 【记录】a 标签下载视频却跳去播放的问题
date: 2019-09-10 20:04:23
categories: 记录
tags: 记录
---

明明写了  a 标签， 也加了 download属性， 但是还是跳去播放视频了， 原来，如果想下载视频， 你得在 a 标签的视频链接地址后面带个参数`?filename=xxx`

比如:

```
www.aaa.com/video?filename=xxx
```















