---
layout: post
title: 树莓派 Rasbian 系统 修改静态/动态ip地址的方法
date: 2022-02-06
author: H_On
description: 很难搜到的难题自己解决了记录一下方法
tag: 树莓派
---

- [前言](#前言)
- [修改方法](#修改方法)

### 前言
其实是 `2020-01-03` 的记录，但是当时没有上传博客，如今整理上传

当时应该是搜集了多方博客，找了半天没找到解决方法，最后摸索出来以后马上记录了一下

### 修改方法
使用 `ifconfig` 命令来查看自己的网卡情况<br>
eth0 - 有线网卡
wlan0 - 无线网卡
编辑 /etc/dhcpcd.conf 这个文件，修改其中的
```
static ip_address=192.168.233.233/24# 设置静态IPv4地址
static routers=192.168.233.1# 设置网关
static domain_name_servers=192.168.233.1# 设置域名服务器地址
```
如果想用动态获取ip，那就直接把静态的设置注释掉就好了
```
# static ip_address=192.168.233.233/24# 设置静态IPv4地址
# static routers=192.168.233.1# 设置网关
# static domain_name_servers=192.168.233.1# 设置域名服务器地址
```
