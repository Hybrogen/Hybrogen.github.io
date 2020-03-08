---
layout: post
title: python 玩玩乐 - moviepy 剪辑视频变成 gif 图
date: 2020-03-08
author: H_On
description: python 的一些有趣的操作
tag: 杂谈
---

此篇来源于被迫营业，但是我很开心

# 本篇附赠解决 RuntimeError: No ffmpeg exe could be found. 报错的方法【可能有效

### 目录
* [前置需要 提示：安装 moviepy 的坑](#前置需要-提示：安装-moviepy-的坑)
* [录制视频](#录制视频)
* [写程序](#写程序)
* [参考文章](#参考文章)

### 前置需要 提示：安装 moviepy 的坑
猹这里依然是在 linux 服务器中进行的操作
1. 首先你得安装 `python` 吧，我这里用的还是 `python3`
2. 然后你需要 `pip` 用来安装第三方库
3. 前两步不会的话看看猹的其他文章获取灵感吖

最后要安装第三方库 `moviepy` 用于剪辑视频
```sh
pip install moviepy
```
![安装moviepy库](/images/20200308/installmoviepy.png)
然而当你导包的时候会有缺少这个东西的提示。。。
```py
import moviepy.editor
```
![出错](/images/20200308/importerror.png)

然而答案 **不是** 重装 moviepy ！也 **不是** 重装 imageio_ffmpeg ！更不是降版本啥的 **都 没 用 ！**<br>
也有见到有人说需要 ~~玄学上网~~ 不过我感觉没安装什么安装不了的东西，主要是没安装这个软件 ⬇ ⬇ ⬇<br>
错误报告都提示了：`No ffmpeg exe could be found.` 为什么百度出来的都没有说让安装这个软件的= =哭了
```sh
apt install ffmpeg
```
那么安装完这个软件以后，一运行，没问题了。我估计这是个视频图形处理什么的比较常用的库，大部分系统可能都自带了，因为我这个 debian 是官方下载的，而且安装的时候除了 ssh 服务什么都没预装 ~ 不然就像这次，说不定要少了解不少东西呢嘿嘿【吐舌头

### 录制视频
很多做过视频或者 up 主的同学可能知道不少软件，obs 真好用但是配置起来太麻烦了<br>
日常临时录制一小段视频的话，这里给大家推荐一个轻量化操作简单的录屏软件
> FSCapture

这个软件好像有免费版，有一些限制（水印？）还是什么的，自己用的话可以去网上找找资源，有钱了记得还是要支持正版吖。<br>
官方英文，不过不要怕，界面很简单，随便查几个单词就能搞懂用法了

### 写程序
有一说一，程序很简单，就是安装那个第三方库给我整的难受半天。<br>
这个 `VideoFileClip` 貌似支持大部分的视频格式，包括我都没用过的，然而一上来就不支持 `.wmv` /笑哭<br>
所以格式工厂转 `.mp4` 再开始整活
```py
# 习惯性的设置编码，以及把主要程序写在一个判断主文件的 if 里，看不惯的同学见谅哈，主要是我不加自己看着难受=w=
#-*- coding:utf-8 -*-

from moviepy.editor import VideoFileClip

if __name__ == '__main__':
    mc = VideoFileClip('./movies/test.mp4')
    mc = mc.subclip((0, 2), (0, 18)).resize((480, 320))
    mc = mc.crop(20, 0, 440, 200)
    mc.write_gif('./pika.gif', fps = 2)
```
1. 这里我们只需要使用包里 `VideoFileClip` 这个类
2. 先使用视频生成一个视频切片 `Movie Clips`
3. 设置截取的视频片段，示例中是截取了 `0 分 2 秒 ~ 0 分 18 秒` 中间的视频，并使用 `.resize()` 方法重新设定了视频的分辨率
4. 由于我录 的视频有一些多余的部分，因此我使用 `.crop()` 方法对所有切片都选取了 `x = 20 ~ 400; y = 0 ~ 200;` 的画面
5. 最后使用 `.write_gif()` 这个方法处理过的视频切片导出成一个 gif 图片

我是运行了 [十四学长的一个有趣的程序](https://www.urlteam.cn/?p=2167) 之后录了一下运行结果，挺有意思的大家可以去康康
![运行皮卡丘](/images/20200308/pika.gif)

### 参考文章
[基于python的moviepy包](https://www.jianshu.com/p/99bf9aad1624)<br>
[python小应用之moviepy的视频剪辑制作gif图](https://blog.csdn.net/Spade_/article/details/79516322?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
