---
layout: post
title: ffmpeg 从安装到把视频旋转 90 度
date: 2022-04-12
author: H_On
description: 记录一下 ffmpeg 的使用和问题
tag: 杂谈
---

- [起因](#起因)
- [安装](#安装)
- [将视频转动 90 度](#将视频转动-90-度)

### 起因
瑾业先锋一大早的过来公司给学员答疑，他说他很难理解一个算法，但是平台的教程没有视频讲解，所以他很难理解、、、
我寻思我就是做视频的，给他录一小段多好，于是研究了一下 ipad 怎么录屏（这个倒是还好说，一搜就有）录了一段。
虽说恩来视频就不大，但是我还是想用小丸压一下，因为录出来的声音怪怪的，主要是用小丸处理一下声音、、、结果压完以后横向的视频变成了纵向（ΩДΩ）
因为公司电脑上没装pr，装一个费老大劲，于是我慌慌张张的发到手机上用一甜转动视频，结果它保持纵向，把视频上下填充了黑条（什么逆天操作）
然后我又发到 sony 手机上，这个手机相册自带转动视频的功能，结果我一转画面糊完了。。。
最后偶然找到一条说用 ffmpeg 命令转动，我寻思这挺方便、、、结果从探索到安装到成功花了一个多小时

### 安装
好多教程都贴了某个官网，但是好像炸了我就不贴了，这个看起来也像官网，是能用的
[ffmpeg 官网](http://ffmpeg.org/download.html#build-windows)，然后这个是编译 github 源码之后 exe 下载的网站
[github 编译版本下载](https://www.gyan.dev/ffmpeg/builds/)

为防再炸，我也保存了写博客时的最新版本 [linux](http://zinchon.com:339/getfile?fn=ffmpeg-4.2.2.zip)、[win.exe](http://zinchon.com:339/getfile?fn=ffmpeg-2022-04-11-git-d6d46a2c50-full_build.7z)

下载解压了以后，配置一下环境变量到 `ffmpeg-2022-04-11-git-d6d46a2c50-full_build\bin` 就可以在 cmd 里使用 ffmpeg 命令了

### 将视频转动 90 度
我没有好好研究这个库，我只想把视频转动 90 度 QAQAQAQAQ【我哭死
```bat
ffmpeg -i in.mp4 -c copy -metadata:s:v:0 rotate=90 out.mp4
```