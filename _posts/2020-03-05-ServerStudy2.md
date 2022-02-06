---
layout: post
title: 服务器学习历程 Part 2 - 拿到一个 linux 服务器应该做什么
date: 2020-03-05
description: Linux 系统的一些有趣的操作
tag: Linux系统
---

目前此文章全都是在 `Ubuntu 16.04` 中的操作

**此篇集成了 公钥登录 OhMyZsh 自定义登录提示 安装python3.6 安装mysql 安装phpmyadmin 安装Scrapy 安装requests，etree**

### 目录
- [目录](#目录)
- [已经登录远程服务器设置公钥登录](#已经登录远程服务器设置公钥登录)
- [使用 Oh My Zsh 优化命令终端](#使用-oh-my-zsh-优化命令终端)
  - [**更换样式**](#更换样式)
  - [**启用插件**](#启用插件)
  - [**其他事项**](#其他事项)
- [添加一点自定义的登录后显示的提示](#添加一点自定义的登录后显示的提示)
- [安装 python3.6](#安装-python36)
- [安装 mysql](#安装-mysql)
- [安装 phpmyadmin](#安装-phpmyadmin)
- [营业](#营业)
  - [**安装 Scrapy**](#安装-scrapy)
  - [**安装网站连接库 requests**](#安装网站连接库-requests)
  - [**安装 html 解析库 etree**](#安装-html-解析库-etree)
- [参考文章](#参考文章)

### 已经登录远程服务器设置公钥登录
因为腾讯云阿里云什么的服务器都不让直接用 root 用户 ssh 登录，然而普通用户操作起来又很难受，尤其是常用 ubuntu 系统，很多 sudo 命令又奇怪的很。所以尝试用公钥私钥的方式直接用 root 用户 ssh 登录。

操作步骤:
1. 首先
```bash
sudo su
```
进入 root 用户
2. 然后
```bash
cd ~
```
进入 root 用户的家目录 `/root/`
3. 使用以下命令生成钥匙对
```bash
ssh-keygen -t rsa -C "双引号里是提示文字随便写，我也不知道有啥用"
```
直接三次回车默认生成在 `/root/.ssh/` 里面就行
   1. 第一次回车是自定义密钥生成的位置和名字，默认 `/root/.ssh/id_rsa` 和 `/root/.ssh/id_rsa.pub`
   2. 第二次第三次回车，输入密码和确认，直接回车就行了，如果输入密码那就变成密钥加密码，还是麻烦。推荐密钥就够了，不要再加密码了
4. 使用命令
```bash
cat id_rsa.pub >> authorized_keys
```
将公钥写入到固定文件里
5. 将 `id_rsa` 下载到本地，如果无法下载先使用 `chmod` 命令修改文件的权限然后再下载
6. 将 ssh 工具的登录方式打开 `使用公钥登录` 之后选定连接用户为 `root` ，选择公钥位置，之后就可以直接用 root 用户 ssh 登录服务器了

### 使用 Oh My Zsh 优化命令终端
可以通过以下命令查看有没有安装
```sh
zsh --version
```
如果没有安装使用如下命令安装，root 用户可以不加 sudo
```sh
sudo apt install zsh
```
之后通过命令安装 Oh My Zsh - 建议查看一下[官方](https://github.com/ohmyzsh/ohmyzsh/)给的命令，这个连接的网址好像变过
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh) -y"
```
![zsh](/images/20200305/zsh1.png)<br>
如果你的系统里没有，需要先安装`git`和`curl`
```sh
apt install git curl -y
```

#### **更换样式**
安装完成以后，首先可以发现 Oh My Zsh 修改了你的命令终端的命令行的样式<br>
你可以通过修改它的配置文件 `~/.zshrc` 来更换样式
```
# 默认样式是 robbyrussell
ZSH_THEME="robbyrussell"
```
你可以使用此命令查看它都内置了那些样式
```sh
ls ~/.oh-my-zsh/themes
```
比如如果你想用 `ys.zsh-theme` 这个样式，你就可以设置成
```
ZSH_THEME="ys"
```
ps: 这个是我用过感觉还不错的，比较犹豫的小伙伴可以先用着这个

你也可以自己下载一些主题
> https://github.com/robbyrussell/oh-my-zsh/wiki/Themes<br>
> https://github.com/robbyrussell/oh-my-zsh/wiki/External-themes<br>
> https://github.com/unixorn/awesome-zsh-plugins#themes

#### **启用插件**
Oh My Zsh 自带了一些插件，可以通过以下命令查看都有哪些插件
```sh
ls ~/.oh-my-zsh/plugins
```
大家自行了解吧，我啥都没用

#### **其他事项**
* 由于这个 `.zshrc` 是登录以后最后运行的，所以它会覆盖掉你的 `.bashrc` 配置的东西，所以类似 `alias ll='ls -alF'` 这种你想要自定义登录运行的东西可以在 `~/.oh-my-zsh/oh-my-zsh.sh` 这个文件最后添加一下
* 如果你想使用回去原来的命令行，输入 `bash` 即可换回去
* 同样的，如果你在普通命令行，输入 `zsh` 即可使用 zsh 命令行

### 添加一点自定义的登录后显示的提示
在 `/etc/motd` 这个文件里写入一些东西，就可以在登录的时候一起输出了
```
////////////////////////////////////////////////////////////////////
//                          _ooOoo_                               //
//                         o8888888o                              //
//                         88" . "88                              //
//                         (| ^_^ |)                              //
//                         O\  =  /O                              //
//                      ____/`---'\____                           //
//                    .'  \\|     |//  `.                         //
//                   /  \\|||  :  |||//  \                        //
//                  /  _||||| -:- |||||-  \                       //
//                  |   | \\\  -  /// |   |                       //
//                  | \_|  ''\---/''  |   |                       //
//                  \  .-\__  `-`  ___/-. /                       //
//                ___`. .'  /--.--\  `. . ___                     //
//              ."" '<  `.___\_<|>_/___.'  >'"".                  //
//            | | :  `- \`.;`\ _ /`;.`/ - ` : | |                 //
//            \  \ `-.   \_ __\ /__ _/   .-` /  /                 //
//      ========`-.____`-.___\_____/___.-`____.-'========         //
//                           `=---='                              //
//      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        //
//         佛祖保佑                     永不死机                  //
////////////////////////////////////////////////////////////////////
```
![fouzu](/images/20200305/login1.png)

我这边使用了别的东西，使用这个命令来输出我的系统 ubuntu 的系统名
```sh
linux_logo -L 30
```
这个命令可以查看都有哪些系统，将上面的命令最后的数字对应替换即可
```sh
linux_logo -f -L list
```
之后讲系统信息写进 `/etc/motd` 这里面就行了
```sh
linux_logo -L 30 > /etc/motd
```
![system-logo](/images/20200305/login2.png)

**或者**也可以安装使用更有趣的软件<br>
`figlet` 是使用键盘上的符号帮你输出一个大字，只支持英文
```sh
apt install figlet
figlet 233lol
figlet "H_On's debian"
```
把这个写进上面说的文件里也很有趣嘿嘿

![custom-logo](/images/20200305/login3.png)


### 安装 python3.6
网上找了一些教程跟着做，这里贴出亲测能用的一个
```sh
wget http://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz
tar -xvzf Python-3.6.4.tgz
cd Python-3.6.4
./configure --with-ssl
# 管理员可以不加 sudo
sudo make
sudo make install
which python3.6 # 查看你的 python3.6 所在位置
# /usr/local/bin/python3.6
which python # 查看你的 python 软连接所在位置
# /usr/bin/python
# 将默认的 python 命令修改成 python3.6
ln -s /usr/local/bin/python3.6 /usr/bin/python -f
# 再次查看 python 命令的版本
python --version
# Python 3.6.4
```
ps：安装完以后刚才下载的这个 `Python-3.6.4.tgz` 和解压出来的这个 `Python-3.6.4/` 都可以删了哈

建议安装：ipython<br>
不知道为什么 `apt install ipython` 不能正常安装使用，`pip3 install jupyter` 却可以= =，安装完 `jupyter` 以后 ipython 命令就可以用了= =

### 安装 mysql
```sh
apt install mysql-server
# 远程访问
apt install mysql-client
apt install libmysqlclient-dev
```

### 安装 phpmyadmin
```sh
apt install phpmyadmin
```
安装过程中就可以直接安装 apach 啦
在浏览器里输入你的 `ip地址/phpmyadmin/` （ip 也可能是 localhost）就可以访问 web 形式的数据库了 例：`localhost/phpmyadmin/`

### 营业
#### **安装 Scrapy**
事前准备【这么老多
```sh
apt install python-dev
apt install build-essential libxml2-dev libxslt1-dev python-setuptools
apt install python3-pip
# 你可能还需要升级一下你的 pip3
pip3 install --upgrade pip
```
终于可以正常安装 Scrapy 了
```sh
pip3 install Scrapy
```
#### **安装网站连接库 requests**
```sh
pip3 install requests
```
#### **安装 html 解析库 etree**
```sh
pip3 install lxml
```
python 中的使用的是
```py
from lxml import etree
```

### 参考文章
[利用Oh-My-Zsh打造你的超级终端](https://blog.csdn.net/u012150590/article/details/79892657)<br>
[ohmyzsh-github](https://github.com/ohmyzsh/ohmyzsh/)<br>
[意想不到的有趣linux命令18个，玩得溜](https://blog.csdn.net/qq_41741971/article/details/82053290)<br>
[Ubuntu16.04下安装python3.6.4详细步骤](https://www.cnblogs.com/aidenzdly/p/11351316.html)
