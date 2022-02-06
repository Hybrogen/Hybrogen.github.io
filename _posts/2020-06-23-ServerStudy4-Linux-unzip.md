---
layout: post
title: 服务器学习历程 Part 3 - Linux解压二三事
date: 2020-06-24
description: 在为了做好自己本职工作的过程中遇到的问题
tag: 杂谈
---

- [zip](#zip)
- [7z](#7z)
- [参考文章](#参考文章)

### zip
`zip`的解压命令就是很简单的
* `unzip filename.zip` 解压到本地
* `unzip -d outdir/ filename.zip` 解压到指定文件夹
* `unzip -o -d outdir/ filename.zip` 直接覆盖不提示的解压到指定文件夹

然后我在解压过程中遇到一个问题，解压出来的文件名中文乱码，最后发现了[参考文章](#参考文章)中的解决方法，实测第二个有点不会用，第一个方法我是可以用的所以这里简单记录一下
`unzip -O cp936 -o -d outdir/ filename.zip`

### 7z
`7za x filename.7z -r -ooutdir/` -o是指定解压文件夹，后面不加空格直接加路径

### 参考文章
* [Linux下的压缩（zip）解压(unzip)缩命令](https://blog.csdn.net/shenyunsese/article/details/17556089)
* [Linux 下 zip 文件解压乱码如何解决？](https://www.zhihu.com/question/20523036)
