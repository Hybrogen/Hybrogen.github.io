---
layout: post
title: Debain10忘记root用户密码怎么办
date: 2020-05-04
author: H_On
description: 开始为了自己研究点东西
tag: 杂谈
---

### 目录
* [前情介绍](#前情介绍)
* [重置密码](#重置密码)
* [参考文章](#参考文章)

### 前情介绍
猹自从学会用公钥登陆以后觉得好香好方便啊，然后今天莫名其妙琢磨着要不要把密码设置的复杂一点，这样别人就登录不了了。<br>
一顿操作猛如虎，完事以后发现自己也登不上了。。。密码肯定没有输入错误，但是无论如何就是不让登录。普通用户登进去以后su也没用，纯净系统又没有装sudo【悲<br>
于是就踏上了一条重置root用户密码之路，，，

### 重置密码
一开始还不知道 grub 界面是啥，原来就是选择启动系统的界面就叫 grub 界面。<br>
先到这个界面，反应慢的同学可以先按着下方向键取消掉自动开机，然后用上下方向键选择 **我们要启动的的系统** 通常就是第一个，网上看到 redcat 通常是第二个，印象中 ubuntu 也是第二个，不过我们就不管辣
![grub页面](/images/20200504/grubpage.png)

选中这个系统之后
> 按 "e" 键进入编辑配置的界面<br>
> 找到 "quiet" 在 "quiet" 后面添加 "init=/bin/bash"

![编辑系统配置](/images/20200504/edit1.png)
![编辑系统配置](/images/20200504/edit2.png)

这之后按一下 `ctrl+x` 启动系统，我们就进入了管理系统
> 注：这个系统虽然是 root 用户，但是所有的文件权限都不能修改，网络也不能连接，所以想要把文件
> * 该权限之后用普通用户拷贝出去
> * 用 scp 工具远程备份出去，或者挂载网盘都是不行的

系统启动完之后是这个界面
![管理界面](/images/20200504/entry.png)

在这个界面就可以用 root 的权限用 `passwd` 命令直接重新设置 root 用户的密码
> 注：如果说 passwd 命令不能用，那就重新挂载一下根目录即可

![操作](/images/20200504/operate.png)

然后发送 `ctrl+alt+delete` 重启系统，就能用 root 用户和刚刚改过的密码登录了。

### 参考文章
[How to Reset Forgotten Root Password in Debian 10](https://www.tecmint.com/reset-forgotten-root-password-in-debian/)<br>
[解决问题：chmod: changing permissions of ‘...': Read-only file system和/dev/sda1 is write-protected but ex](https://blog.csdn.net/zhangpeterx/article/details/83928344)
