---
layout: post
title: 七日杀 7 days to die 点击玩家档案报错，无法创建修改
date: 2023-03-19
author: H_On
description: 七日杀联机报错解决的路上
tag: 游戏
---

### 目录
- [目录](#目录)
- [问题](#问题)
- [解决方法](#解决方法)
- [服务器拒绝连接解决方法](#服务器拒绝连接解决方法)

### 问题
联机生成玩家时报错 `NullReferenceException: Object reference not set to an instance of an object` 有人说可以删玩家档，然而并不行。有人说可以删档后重新创建一个档案再进服。然而当我点击玩家档案时报错 `EXC IndexOutOfRangeException:Index was outside the bounds of the array`

### 解决方法
经过一上午的重启和删除，卸载游戏重装都没用之后，偶然在 [这篇贴吧](https://tieba.baidu.com/p/7918495463) 中发现楼主的一个 [回复截图](https://tieba.baidu.com/photo/p?kw=%E4%B8%83%E6%97%A5%E6%9D%80&flux=1&tid=7918495463&pic_id=e05cca94a4c27d1ee3a3d21e5ed5ad6edfc438c2&pn=1&fp=2&see_lz=1&post_id=144724158913)

* `Win + R` 打开运行
* 输入 `regedit` 打开注册表
* 按顺序打开 `计算机\HKEY_CURRENT_USER\SOFTWARE\The Fun Pimps`
* 删除里面的 `7 days to Die`
* 再次进入游戏

此时会发现进入游戏以后，点击选项 -> 玩家档案，已经可以正常查看预设形象和创建新的形象了。

### 服务器拒绝连接解决方法
如果服务器开了 EAC 保护，那么客户端也要开，如果服务器没开 EAC 保护，客户端也不能开。否则就进不去服务器。

如果服务器装了 mod 那么 maybe 客户端也要装。