---
layout: post
title: 树莓派滞销，救救我们！【通过nginx和frp反代理让内网树莓派变成一个前后端网站服务器
date: 2020-10-17
description: 第一次做涉及这么多技术的系统w
tag: 树莓派
---

- [事前准备](#事前准备)
- [搭建前后端分离的网站服务器](#搭建前后端分离的网站服务器)
  - [使用django搭建的后端服务器](#使用django搭建的后端服务器)
- [开始构建frp内网穿透](#开始构建frp内网穿透)
  - [配置客户端-在树莓派上搭建frp客户端](#配置客户端-在树莓派上搭建frp客户端)
  - [配置服务端-在具有公网ip的vps上搭建frp服务端](#配置服务端-在具有公网ip的vps上搭建frp服务端)
- [代理完成](#代理完成)
- [总结-无域名frp内网web服务器](#总结-无域名frp内网web服务器)
- [扩展-使用域名进行http代理](#扩展-使用域名进行http代理)
- [扩展2-什么？你有同时使用nginx or 多个域名的需求？](#扩展2-什么你有同时使用nginx-or-多个域名的需求)

xx云太贵？国外便宜的vps性能又差连接速度又慢？手头居然还有一个树莓派在吃灰！这前两项用钱就能解决~~虽然我没钱~~但是最后一项不能容忍，可爱的树莓派怎么可以放置play。<br>
快来跟着猹猹一起拯救我们可怜的树莓派吧！

### 事前准备

* 一个树莓派【of course
  * 安装django
  * 安装nginx
* 个人域名【可以不要
* 国内/国外vps【必要，作为内网穿透服务器

**开工！**

### 搭建前后端分离的网站服务器

后端由[猹](https://blog.zinchon.cn/)来实现，前端是由[十三里铺](https://me.csdn.net/weixin_43736435)实现<br>
django虽然是个全栈框架，但是如果前后端都坐在django里其实是很麻烦的~~另外猹不太会前端w~~，所以我们采用前后端分离的做法，一是数据交换更简单，二是现在虽然都放在猹的树莓派上，但是后期分离前后端服务器也方便。

#### 使用django搭建的后端服务器

django实在简单，猹这种没有网站开发经验的小白也能一下实现简单的功能<br>
新建一个空文件夹，安装好了django之后就可以开始新建一个django项目了

1. `django-admin.py startproject xxx` 新建一个项目，xxx是你的项目名称，可以修改
   一个默认django项目的文件结构是这样的
   ```
    xxx
    ├── manage.py
    └── xxx
        ├── asgi.py
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
   ```
2. 首先修改文件`settings.py`中的`ALLOWED_HOSTS = ['*']`，*表示任何ip都可以访问，也可以写一个或多个指定的访问ip或域名，中间用逗号分隔。比如这里可以写成`ALLOWED_HOSTS = ['ewc.zinchon.net','127.0.0.1','xxx.xxx.xxx.xxx']`
   1. `ewc.zinchon.net` - 是猹通过frp反代理过来的域名，如果想通过这个地址访问，就必须把这个域名加入`ALLOWED_HOSTS`
   2. `127.0.0.1` - 这是本地测试的时候使用的地址，如果树莓派没有图形界面，就把树莓派的内网ip放进去，然后局域网内其他电脑访问
   3. `xxx.xxx.xxx.xxx` - 这是一个ip地址，跟`ewc.zinchon.net`这个一样，没有域名的朋友在后面配置完以后，通过这个ip访问，这个ip实际上就是你的frp服务器的外网地址
   配置完这几步，你就可以进入项目根目录（就是有`manage.py`的那一层目录）通过命令`python3 manage.py runserver 0.0.0.0:8000`来启动django服务器<br>
   在浏览器输入`127.0.0.1:8000`就可以看到一个默认的首页了
3. 在文件夹`xxx`中新建一个文件`views.py`，现在的文件结构是这样的
   ```
    xxx
    ├── manage.py
    └── xxx
        ├── asgi.py
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        ├── views.py
        └── wsgi.py
   ```
   这是添加自定义web方法的文件，在里面写上一些简单的函数来测试一下<br>
   这里直接给出一个前后端分离的时候使用的返回数据的demo，通常使用json来交换数据
   ```py
   from django.shortcuts import HttpResponse
   from django.http import JsonResponse

   import json

   def index(request):
      return HttpResponse('自定义一些文本')

   def data(request):
      data = {'数据是这样返回的'：'通过一个解析成json的字典对象'}
      # 常用的httpResponse，在返回数据的时候需要把字典解析成json"字符串"并且指定返回的文本类型
      return HttpResponse(json.dumps(data), content_type='application/json')
      # 也可以使用HttpResponse的一个子类JsonResponse，记得在头文件里引入对应的类
      # 使用JsonResponse可以直接返回字典对象，而不用再通过json库进行转换
      return JsonResponse(data)
   ```
4. 修改文件`urls.py`添加一个访问路径，记得把自己定义的`views`导入
   ```py
    from django.contrib import admin
    from django.urls import path

    from . import views

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', views.index),
        path('data', views.data),
    ]
    ```
    * 这里的请求路径为空，即`ip[:端口]`访问的返回页面
    * 如果要新增一个页面，请求路径里写`path('/xxx', views.method),`
    * 如果是一个请求，请求路径里写`path('xxx', views.method),`

如果你的服务没有停止，每次更新配置文件夹xxx中的文件时，它都会自动重启，这时候你再次访问`127.0.0.1:8000`就会发现它显示了你自定义的内容<br>
访问`127.0.0.1:8000/data`就可以看到自定义返回的数据

> 注意，如果你想使用80端口，那么你需要ctrl+c关闭django服务，并运行命令`python3 manage.py runserver 0.0.0.0:80`来启动，**后面的端口不能省略**，访问页面就可以改成`127.0.0.1`了

#### 搭建前端服务器

前端是使用vue做的，具体原理就不介绍了，这里只介绍如何使用

1. 下载十三里铺打包好的页面，放到`nginx/html`中
2. 在`nginx/conf.d`中添加对应的配置文件`xxx.conf`
   ```conf
   server {
        listen 1233;
        server_name     xxx.xxx.xxx.xxx;
        location / {
                root   /etc/nginx/html/xxx/;
        }
        location /data {
                add_header 'Access-Control-Allow-Origin' $http_origin;
                add_header 'Access-Control-Allow-Credentials' 'true';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'DNT,web-token,app-token,Authorization,Accept,Origin,Keep-Alive,User-Agent,X-Mx-ReqToken,X-Data-Type,X-Auth-Token,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
                add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';
                if ($request_method = 'OPTIONS') {
                        add_header 'Access-Control-Max-Age' 1728000;
                        add_header 'Content-Type' 'text/plain; charset=utf-8';
                        add_header 'Content-Length' 0;
                        return 204;
                }
                root   /etc/nginx/html/xxx/;
                index  index.html index.htm;
                proxy_pass http://127.0.0.1:1234/data;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_connect_timeout 5;
        }
   ```
   保存之后重启nginx服务`/usr/sbin/nginx -s reload`

   ps:
   1. 有的时候nginx程序会放在`/etc/nginx/sbin/nginx`，或许别的神奇地方，你可能需要找一下
   2. `server_name`是服务器的ip，你可以写frp服务器的ip地址或是写上frp服务转发到这个端口的域名
   3. 请求`data`是在web服务中写的请求名，转发规则中`proxy_pass http://127.0.0.1:1234/data;`的`data`是请求后端服务器的的请求名，两个可以不一样，但请尽量一样。这样做可以达成`前端服务器ip[:前端服务端口]/data`和`后端服务器ip[:后端服务端口]/data`结果一致的效果
   4. 记得后端django服务要开在1234端口
3. 这时候你就可以在浏览器输入`http://127.0.0.1:1233`来查看你的网页是否能运行并且是否能正确请求到后端数据了~

### 开始构建frp内网穿透

frp是一个内网穿透技术，也有说是一种反代理技术，它是客户端向服务器发送连接请求，这样我们就可以通过存在于外网的服务器直接访问到内网主机了。<br>
支持多种服务，常用的有ssh，ftp，http等...

推荐大家观看[官方文档](https://github.com/fatedier/frp)，有[中文](https://gofrp.org/docs/)而且讲解很详细，这里猹只介绍简单的应用

#### 配置客户端-在树莓派上搭建frp客户端

[官方地址](https://github.com/fatedier/frp/releases/)中下载`frp_0.34.1_linux_arm.tar.gz`，因为树莓派是基于arm芯片开发的单片机，而且官方推荐的rasbian系统默认是32位的，如何打开树莓派的世界请观看[初见树莓派](https://blog.zinchon.cn/2019/12/RaspberrypiSystemInstall/)<br>
接下来猹默认用root用户了，如果不想用root用户请在各个命令前面添加sudo<br>
新建一个文件夹
```sh
mkdir /etc/frp; cd /etc/frp
```
并且把下载下来的文件`frpc`和`frpc.ini`上传进去

现在需要两步操作:
1. 修改frpc这个文件的运行权限
   ```sh
   chmod 777 frpc
   ```
2. 修改frpc.ini的内容
   ```
   [common]
   server_addr = xxx.xxx.xxx.xxx
   server_port = 1230

   [ssh]
   type = tcp
   local_ip = 127.0.0.1
   local_port = 22
   remote_port = 1232

   [web]
   type = tcp
   local_port = 1233
   remote_port = 1231
   ```

接下来在frp的文件夹里使用命令开启frp
```sh
./frpc -c frpc.ini
```
现在启动可能会失败，因为服务器上还没有配置frp服务，请暂且记下这个启动命令，因为到此为止客户端的配置全部完成，之后服务器搞完以后只需要在客户端启动一下frp即可

#### 配置服务端-在具有公网ip的vps上搭建frp服务端

无论是linux系统，还是windows系统，亦或是一个树莓派，只要拥有公网ip就可以作为我们的frp服务器【拥有公网ip的树莓派？这也太魔幻了

下载对应版本的frp，跟上面一样，比如猹用的是64位的debian系统，所以就下载`frp_0.34.1_linux_amd64.tar.gz`这个版本，然后把里面的`frps`和`frps.ini`放到我的vps的`/etc/frp`里<br>
同样的修改权限，然后修改ini配置文件设置监听的端口
```conf
[common]
bind_port = 1230
```
通过命令启动
```sh
./frps -c frps.ini
```
之后在你的树莓派上启动frp，开始愉快www

### 代理完成

先用ssh测试一下frp服务器是否顺利运行
> 地址是：xxx.xxx.xxx.xxx - 这是你vps的外网地址
> 端口就是1232

接下来测试一下你的网站嗷~地址是 **xxx.xxx.xxx.xxx:1231**

### 总结-无域名frp内网web服务器
需要：
1. 建立在内网运行web服务的主机
   * raspberrypi zero - 99~144元
   * raspberrypi 3b - 225~243元
   * raspberrypi 4b - 255~545元
   > 这里拯救可怜的树莓派，猹使用的树莓派是4b，4G内存的（大概380元），搭建的是一个物联网应用的可以通过页面控制加水制冷的温度检测系统，涉及操作io口的硬件控制，主要是自己做着玩，所以需要的性能很少，~~或许raspberrypi zero都可以运行~~ 有一说一树莓派比起那些昂贵的云服务器性价比要高太多了，但从价格上来说就便宜好多。而且也灵活，数据也安全，玩起来也趣味无穷有很多玩法，可以说是很值了
2. 运行在外网的frp服务器，用于实现内网穿透
   * 有白嫖的，一般不太可，亚马逊什么的还可以，但是白嫖之路漫漫
   * 便宜的有几块钱一个月到一年300以内的，卖云服务器的商家有很多，大家可以多尝试最后找到最好的，如果不想尝试就百度，知乎啥的看别人推荐吧
   > vps这方面的价格参差不齐，实际上我们只搭建frp或者nginx这些反代理服务，尤其是自用，需要的性能非常低，所以完全可以购买一个价格低廉的服务器，但最主要的是稳定性，尽量不要选购那些被封的很快或是经常连接不上的网络连接情况很差的国内外vps

端口：<br>
大家看上面的教程可能会觉得端口乱七八糟的，猹一开始也很容易搞混，为了照顾小白这里解释一下各个端口，给大家做个注释
* 1230 - frp服务器监听端口，也就是服务器是通过1230这个端口为客户端提供服务的，但并不是我们使用的端口，是frp客户端向服务器发送连接请求使用的端口
* 1231 - 前端服务器的端口，客户端通过1230这个端口连接服务器，并且告诉服务器“我要通过1231这个端口连接这个服务”，于是服务器就开放了自己的1231端口给用户使用，当我们通过1231这个端口连接服务器时，frp服务就会把从1231这个端口发过来的请求传给客户端使用，客户端返回到服务器的数据也会通过1231端口发送给用户
* 1232 - ssh连接跟上面是同理
* 1233 - 本地web服务开放的端口，当我们用内网访问“内网ip:1233”的时候就是在访问树莓派的1233端口，在这个端口上开放的就前端服务器，要注意1233和1231虽然写在一起，但并不是在自己身上开了个1231端口，而是客户端 **让** 服务器通过1231接受这个服务的请求，所以理论上设置成一样的并不会冲突，这里猹不想测试了，大家感兴趣可以自己折腾哈wo
* 1234 - 后端服务开放的端口，这里前端服务把自己的请求转发到后端服务器，虽然这篇文章介绍的前后端服务都在一起，但是上面猹也解释了，前后端分离有利于以后两端随便乱放，所以这个端口不用在意，我们只需要知道web服务是开放在 **1233** 端口，frp代理的也是1233端口就好

### 扩展-使用域名进行http代理
稍微懂一点的同学就会发现，frp里内网代理网站实际上是基于tcp协议通过端口转发的，这样虽然可以，但只能说是没有域名是选用的备选方案，毕竟有域名的同学谁愿意访问自己网站的时候还要在浏览器里输入丑丑的ip地址呢【除非你的ip很美丽，那当我没说

这里插入一句，猹也是最近才知道，域名只有实名认证之后才能在DNS服务器中解析ip，不过实名认证很容易辣。各种云和域名平台都有很廉价的域名，现在比较流行的xyz后缀的域名通常很便宜，而且也很俏皮，用于自己的朋友圈流传挺好的，有的网站第一年只要几块甚至几毛，所以强烈有心情捣鼓的优先搞个属于自己的域名

这时候，首先在域名管理里添加一个**A记录**，把你的所有二级域名都解析到你的vps上
```
A - *.domain.xyz - xxx.xxx.xxx.xxx
```
接下来修改你的服务器frp配置文件
```ini
[common]
bind_port = 1230
vhost_http_port = 1238
subdomain_host = domain.xyz
```
然后修改你的客户端frp配置文件
```ini
[common]
server_addr = xxx.xxx.xxx.xxx
server_port = 1230

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 1232

[web]
type = http
local_port = 1233
subdomain = sub
```
这时你就会发现，web服务的服务类型变成了`http`，服务端新增一个监听的端口`1238`，这个端口是专门用来监听http服务的，然后虽然我们是把所有的**二级域名**解析到这个服务器上了，但是这里的域名主机要写域名本体，在客户端上把子域名写成你想使用的二级域名的名字<br>
这时你就会可以使用`sub.zinchon.xyz:1238`来访问你的内网服务器辣，端口也丑？好说！<br>
服务端的配置文件里，把监听http服务的端口改成80即可。大家都是成年人，懂得都懂，不懂的。。。我还是解释一下吧wo<br>
http服务的默认端口就是80，所以当你输入一个网址其实不是用网址就可以访问，而是默认通过80端口来访问，所以`sub.zinchon.xyz == sub.zinchon.xyz:80`

### 扩展2-什么？你有同时使用nginx or 多个域名的需求？
当你需要多种反代理服务，但都想用80端口的时候，猹用了个骚操作w<br>
nginx支持通过域名反代理，所以我可以这样给我云服务器上安装的nginx写三个配置文件
```conf
# xyz.conf
server {
        listen          80;
        server_name     *.domain.xyz;

        location / {
                proxy_pass http://127.0.0.1:1238;
                proxy_set_header    Host            $host:80;
                proxy_set_header    X-Real-IP       $remote_addr;
                proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_hide_header   X-Powered-By;
        }
}

# baidu.conf
server {
        listen          80;
        server_name     bd.domain.xyz;

        return 301 https://www.baidu.com/;
}
# biying.conf
server {
        listen          80;
        server_name     by.domain.com;

        return 301 https://cn.bing.com/;
}
```
可以看到，依然可以用80端口访问这个服务器，而
* 第一个文件把`domain.xyz`这个域名的所有后缀都转发到本地的1238端口了，也就是frp监听的http端口，这样一来访问80也和之前访问1238一样辣，而且也不影响其他两个代理
* 一个是把`domain.xyz`这个域名的一个二级域名`bd`直接转发到百度的官网
* 第二个是把同样解析到这个服务器的`domain.com`域名的一个二级域名`by`转发到必应搜索的官网

像这样的本机端口代理特别好用，而且能灵活掌控我们的域名，访问网站时不仅不用ip甚至不需要端口，美观大气，这就是 **猹** 对于 **用户体验** 的极致追求【这追求可太low了哈哈

今天的故事就讲到这里辣，祝大家折腾整活开心又顺利
