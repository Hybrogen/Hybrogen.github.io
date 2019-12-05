---
layout: post
title: 基于树莓派的远程温度监测系统
date: 2019-11-25
description: 使用树莓派获取温度并上传到云数据库
tag: 杂谈
---

# 基于 树莓派 3B RS、DHT11/DHT22、Python、MySQL 的远程温度监测系统
# 使用树莓派获取温度并上传至云数据库
# 以及完成程序开机自启

[参考文章](https://www.cnblogs.com/junjun001/p/9335246.html)

ps: 此篇文章只涉及硬件部分的温度获取和上传数据，web 方面的浏览页面的组建可以期待庞大佬的更新文章【他并不喜欢写博客所以不一定会有【吐舌头

### 硬件准备
1. 一个自己管理的路由器（为了能够查看树莓派的 ip 方便之后 SSH），连接路由器的充电器和网线
2. 树莓派 3B RS英国版、树莓派的充电器和网线、装树莓派系统用的 SD 卡
3. DHT11 或者 DHT22 温湿度传感器一个
4. 4.7KΩ 电阻一个
5. 杜邦线若干（其实三根母对母的就够了，后面会讲）
6. 有外网 ip 的云数据库一个（自己映射的也行吧应该）

ps:
1. 以上设备只是我用的是这些，对硬件原理比较懂的也可以自行更换，树莓派的话什么版本的应该不影响，这里我只是把我的型号说一下以供参考
2. 怎么烧树莓派系统和开启 SSH 功能请看猹的另一篇文章 [莓派的系统 TF 卡制作，添加 SSH 功能以及 root 用户 ssh 登陆](https://hybrogen.github.io/2019/12/RaspberrypiSystemInstall/)
3. DHT11 用热敏电阻，精度低，十元以内；DHT22 用热敏电容，精度高，将近三十。原教程用的是 DHT22 然而我第一次做也不敢乱换，就两个都买了，发现效果是一样的，所以自己做着尝试的话推荐先买个便宜的玩玩吧，自己摸索也不需要精度太高
4. 教程上说电阻的阻值是 4.7 或者 10 都行，但是我也不太懂，觉得既然是并联那么就有分流功能，并联电阻越小可能芯片上电流越小然后越安全吧，所以就选用了 4.7KΩ 的哈哈

### 软件准备
ps: <br>
* 下面的操作命令我都加上了 sudo ，即临时获得管理员权限，如果你已经是 root 用户登录那就可以不加 sudo 。如果你就是不想加 sudo 可以使用以下命令来切换到 root 用户然后进行操作；
* 安装命令执行失败的话检查一下网络情况，如果失败了的话就再试几次。
```
~$ su
```
还有操作过程中可能会需要进入其他文件夹 `cd ..` 可以回到 **上一级** 目录 `cd ~` 可以回到 **家目录**
```
~/a/b/c$ cd ..
~/a/b$
~/a/b$ cd ~
~$
```
[**若想跳过繁琐的介绍可以直接去展示所有命令的地方**](#orders1)

##### 1. 有事没事先吧自己的软件都更新一遍
```
~$ sudo apt update
~$ sudo apt upgrade
```

##### 2.检查 python 环境，python 和 python3 都可以，选择其中一个即可，即下面的所有关于 Python 的命令统一使用一套的就可以了【俺喜欢用 python3
检查 python 和 pip 版本：
```
~$ python --version
~$ pip --version
```
如果是 python3 则使用：
```
~$ python3 --version
~$ pip3 --version
```
没有的话就用以下命令安装：
```
~$ sudo apt install python
~$ sudo apt install python-pip
```
如果是 python3 则使用：
```
~$ sudo apt install python3
~$ sudo apt install python3-pip
```

##### 3. 安装前置软件包
```
~$ sudo apt install build-essential python-dev
```
* build-essential 是帮助编译程序的
* python-dev 是用来支持本地安装 python 第三方库的

##### 4. 安装 Adafruit 提供的 DHT 模块的 Python 驱动
检查 git 版本：
```
~$ git --version
```
没有 git 的话用下面的命令安装：
```
~$ sudo apt install git
```
使用 git 命令来获取模块：
```
~$ git clone https://github.com/adafruit/Adafruit_Python_DHT.git
```
进入文件夹进行模块安装：
```
~$ cd Adafruit_Python_DHT
```
安装到 python2 运行库：
```
~/Adafruit_Python_DHT$ sudo python setup.py install
```
如果是 python3 则使用：
```
~/Adafruit_Python_DHT$ sudo python3 setup.py install
```

##### 5. 安装上传数据库所需要的 Python 第三方库 pymysql
python 安装
```
~$ sudo pip install PyMySQL
```
python3 安装
```
~$ sudo pip3 install PyMySQL
```

所有命令展示：（这里就直接按猹的情况来展示了）<div id="orders1"></div>
```
~$ sudo apt update
~$ sudo apt upgrade
~$ python3 --version
~$ sudo apt install build-essential python-dev
~$ sudo apt install git
~$ git clone https://github.com/adafruit/Adafruit_Python_DHT.git
~$ cd Adafruit_Python_DHT/
~/Adafruit_Python_DHT$ sudo python3 setup.py install
~/Adafruit_Python_DHT$ cd ~
~$ sudo apt install python3-pip
~$ sudo pip3 install PyMySQL
```

### 硬件连接
借用[ 这里 ](https://www.cnblogs.com/junjun001/p/9335246.html)的接线图给大家看一下理论上怎么接线

![主板引脚演示](/images/20191125/boardpin.jpg)<br>
![接线演示](/images/20191125/linkline.png)

首先可以用一个面包板来测试一下连接，不过说实话面包板看起来也比较难理清线路，所以这里我拿了几个公对母的杜邦线和几个带线鳄鱼夹来连接了一个简单易懂的实体电路

要注意 DHT11 和 DHT22 的正负极是反的哟~

![两个芯片](/images/20191125/twochip.jpg)<br>
![H_On 的演示 - DHT11](/images/20191125/dht11linkline.jpg)<br>
![H_On 的演示 - DHT22](/images/20191125/dht22linkline.jpg)

顺便给你们康康猹的连线，直接撕破杜邦线的胶皮然后把电阻直接并联（焊）上去的

![H_On 的接线](/images/20191125/honlinkline.jpg)

然后给树莓派插上网线通上电就可以开始测试软件程序辣~

### 测试程序代码
退出到家目录然后新建一个文件方便管理
```
~$ cd ~
~$ mkdir Temperature
~$ cd Temperature
~/Temperature$ vim get_info.py
```
没有 vim 的同学使用下面的命令来安装
```
~$ sudo apt install vim
```
`vim get_info.py` 之后开始编辑测试代码<br>
先整一个简单的小程序来测试一下温度是否能正常获取
```
# 调用可以调用 DHT 模块的函数库
import Adafruit_DHT
# time 库是为了使用里面的 sleep() 延时函数
import time

# 声明一个传感器对象
sensor = Adafruit_DHT.DHT11
# 如果是 DHT22 模块的话就写 DHT22
# sensor = Adafruit_DHT.DHT22
# 使用树莓派引脚 4
pin = 4 #GPIO4

# 循环测 10 次温度
for i in range(10):
    # 使用传感器获取湿度（h）和温度（t）
    h, t = Adafruit_DHT.read_retry(sensor, pin)
    # 如果获取到的温度或湿度有问题，那就不输出，没问题就输出
    if h is not None and t is not None and t > -271 and t < 100:
        print('Temp = {}°C  Humidity = {}%'.format(t, h))
    else:
        print('Failed to get data.')
    # 延时 1 秒
    time.sleep(1)
```
vim 操作不太熟练的同学可以去康康猹的 [Linux基础操作文章](https://hybrogen.github.io/2019/09/ServerStudy-Linux/) 下面有关于 vim 的基础操作【都是干货！都是我经常用的，靠脑子想着写上去哒~像个推销=w=<br>
然后运行一下这个程序试试
```
~/Temperature$ python get_info.py
```
如果是 python3
```
~/Temperature$ python3 get_info.py
```
运行结果：<br>
![运行演示 - 1](/images/20191125/show1.png)

幸运的是居然刚好有一次获取失败了，正好演示出来了一下。由于我后台其实也在运行一个一分钟上传一次数据库的程序，推测可能是同时获取导致获取失败了 233

这里我们可以看到传感器已经正常工作了，如果不正常工作的话检查一下连线是否有问题或者松动没连好<br>
下一步我们就要讲数据好好的整理一下然后上传到数据库了

### 正式程序代码
##### 习惯性设置编码：
```
#-*- coding:utf-8 -*-
```

##### 首先头文件里需要：
* 传感器连接库
* 数据库连接库
* time 库用于延时和获取时间

```
import Adafruit_DHT
import pymysql
import time
```

##### 主函数里准备好不需要经常变化的变量：
* 传感器对象
* 接受信号的引脚编号
* 与数据库的连接

然后获取数据并上传数据库
```
def main():
    # 生成 dht11 温湿度传感器收取对象
    tre = Adafruit_DHT.DHT11
    # 设置信号收取引脚 - GPIO 4
    pin = 4
    # 建立连接
    con = pymysql.connect(
            host = '[数据库 ip 地址]',
            port = [数据库端口 通常是3306],
            user = '[用户名 一般用 root 登陆]',
            passwd = '[对应用户密码]',
            db = '[数据库的名字]',
            charset = '[数据库编码 通常是 utf8 ]'
            )
    while True:
        # 获取温度信息
        data = get_temperature_data(tre, pin)
        # 获取失败了的话输出提醒
        if not data:
            print('get data failed...')
            time.sleep(60)
            continue
        # 将数据上传到数据库
        update_data_to_mysql(con, data)
        time.sleep(60)
```

##### 获取温度信息的函数 get_temperature_data(tre, pin) ：
需要获取传感器收集的数据，还要获取收集到数据的时间
```
def get_temperature_data(tre, pin):
    # 尝试获取三次
    for i in range(3):
        # 使用 read_retry(sensor, pin) 获取传感器收集到的数据
        hum, tem = Adafruit_DHT.read_retry(tre, pin)
        if hum is not None and tem is not None and tem > -271 and tem < 100:
            # 如果传感器获取数据成功，生成一个获取到的时间，格式化成 “年-月-日 时:分:秒”
            tim = time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))
            # print('温度获取成功')
            # print('Temp = ' + str(tem) + '\nHumidity = ' + str(hum) + '\ntime is ' + tim + '\n--------------')
            # 返回一个元组类型的数据，包含 温度 和 时间 ，都是字符串类型
            return (str(tem), tim)
        # 前两次尝试获取失败延时 3 秒重新获取
        if i < 2:
            # print('温度获取wrong，正在尝试重新获取 . . .')
            time.sleep(3)
    # 三次获取失败直接返回一个空数据
    # print('温度获取失败，请检查硬件是否异常')
    return ()
```

##### 将数据上传数据库的函数 update_data_to_mysql(con, data) ：
由于我们的数据库有三个字段：
* 温度
* 标志【因为有多个测温度的地点
* 时间

都是字符串类型
```
def update_data_to_mysql(con, data):
    # 建立游标
    cur = con.cursor()
    # 执行 SQL 语句
    sql  = "INSERT INTO wendu VALUES(%s,%s,%s)"
    # 执行 SQL 语句
    cur.execute(sql, (data[0], 1, data[1]))
    # 提交数据库更改操作
    con.commit()
    # 下面几行是测试查看数据库中数据是否成功添加
    # cur.execute('SELECT *FROM wendu')
    # inf = cur.fetchall()
    # for i in inf:
    #     print(i)
```

##### 最后运行一下 main() 函数即可：
```
main()
```

### 完整代码
```
#-*- coding:utf-8 -*-

import Adafruit_DHT
import pymysql
import time

def get_temperature_data(tre, pin):
    for i in range(3):
        hum, tem = Adafruit_DHT.read_retry(tre, pin)
        if hum is not None and tem is not None and tem > -271 and tem < 100:
            tim = time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))
            return (str(tem), tim)
        if i < 2:
            time.sleep(3)
    return ()

def update_data_to_mysql(con, data):
    cur = con.cursor()
    sql  = "INSERT INTO wendu VALUES(%s,%s,%s)"
    cur.execute(sql, (data[0], 1, data[1]))
    con.commit()

def main():
    tre = Adafruit_DHT.DHT11
    pin = 4
    con = pymysql.connect(host = '0.0.0.0', port = 3306, user = 'root', passwd = '', db = 'gwd', charset = 'utf8')
    while True:
        data = get_temperature_data(tre, pin)
        if not data:
            continue
        update_data_to_mysql(con, data)
        time.sleep(60)

main()
```

### 开机自动运行程序
网上搜 **开机自动启动程序** 一搜一大把，我这里就是简单介绍一下我最后选择使用的自启方式

修改 `根目录下的，etc 目录下的，rc.local 文件` ，在 `exit 0` 前面添加一行
```
python 家目录/Temperature/get_info.py
```
python3 使用：
```
python3 家目录/Temperature/get_info.py
```
指向的文件路径一定要是绝对路径，即从根目录开始的一个路径<br>
比如我使用 root 用户操作的，root 用户的家目录就是 `\root` <br>
那就写：
```
python3 /root/Temperature/get_info.py
```
最后大概长这样：<br>
![rc.local 文件修改展示](/images/20191125/show2.png)

到此为止，整个系统就结束了，由于这是个人博客，所以没什么交流方式，不过在页面下方可以去我的各种其他的社交平台与我联系吖嘿嘿嘿<br>
[这是同步更新的 csdn 的文章地址](https://blog.csdn.net/qq_37504214/article/details/103245959) 可以康康
