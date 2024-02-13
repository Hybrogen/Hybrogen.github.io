---
layout: post
title: 在Debain10上安装和配置Mysql【Navicat远程连接10060错误和1130错误
date: 2020-05-02
author: H_On
description: 开始为了自己研究点东西
tag: Linux系统
---

### 目录
- [目录](#目录)
- [前置需要](#前置需要)
- [安装 Mysql](#安装-mysql)
- [修改用户权限实现**远程连接**](#修改用户权限实现远程连接)
- [打开服务器 Mysql 使用的 3306 端口](#打开服务器-mysql-使用的-3306-端口)
- [参考文章](#参考文章)

### 前置需要
* 首先当然是安装一个 Debian10 系统了~我这里是在虚拟机里操作的，直接安装在主机上应该是效果一样的<br>
  - 如果网络有问题请看猹的另一篇文章[如何修改Debian10的网络]()
  - 当然，如果新安装一个系统，还是推荐用 `oh-my-zsh` 先美化一下，参考->[拿到一个 linux 服务器应该做什么](https://blog.zinchon.cn/2020/03/ServerStudy2/)
* 纯净版的 Debian10 是很多软件都没有预装的，为了下载网络资源，我们需要先安装一个应用 `wget`
  ```sh
  apt install wget
  ```

### 安装 Mysql
这个系统不能直接安装，需要先自己添加一下 mysql 的安装包<br>
用这个命令将 mysql8 的安装包下载到本地并安装
```sh
wget http://repo.mysql.com/mysql-apt-config_0.8.13-1_all.deb
apt install ./mysql-apt-config_0.8.13-1_all.deb
apt update
```
这样我们就能正常安装 mysql-server 辣<br>
过程中设置的 root 用户的密码不要忘记了~
```sh
apt install mysql-server
```
查看一下 mysql 的运行状态
```sh
systemctl status mysql
```
可以用 **service** 命令来开关数据库
```sh
# 停止 mysql
service mysql stop
# 开启 mysql
service mysql start
# 重启 mysql
service mysql restart
```

### 修改用户权限实现**远程连接**
在服务器上使用命令操作数据库，修改用户的通过主机来使用户可以远程连接<br>
服务器拒绝连接会出现 1130 的错误
```sh
mysql -u root -p
# 输入密码进入操作 mysql
```
进入 mysql 后进行如下操作
* 操作数据库改为 mysql 这个库
* 从表 user 中查看用户和支持的主机，默认只支持 localhost
* 修改 root 用户的登录主机为 “任意主机”
* 再次查看观察到修改成功

```sql
use mysql;
select User,Host from user;
update user set host = '%' where user = 'root';
select User,Host from user;
exit
```
重启 mysql 服务
```sh
service mysql restart
```

### 打开服务器 Mysql 使用的 3306 端口
Mysql 的连接默认使用 3306 端口，如果服务器没有对外打开 3306 端口，则需要使用如下命令打开服务器的 3306<br>
**如果 Navicat 连接数据库出现 10060 错误，就尝试打开端口试试**
```sh
iptables -I INPUT 1 -i eth0 -p tcp --dport 3306 -j ACCEPT
```

Navicat 用法很简单的，怎么用它连接数据库这里就不说辣~

### 参考文章
[如何在 Debian 10 Linux 上安装MySQL](https://www.linuxidc.com/Linux/2019-08/159844.htm)<br>
[远程连接MYSQL提示Host is not allowed to connect to this MySQL server](https://blog.csdn.net/EI__Nino/article/details/25069391)<br>
[navicat远程连接mysql10060](https://blog.csdn.net/weixin_30535913/article/details/96897991)
