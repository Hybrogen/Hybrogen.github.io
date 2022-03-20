---
layout: post
title: 用树莓派做一个自动点击手机器【rpi.gpio 库的安装，MG996R舵机和28BYJ-48-5V步进电机的使用教程
date: 2020-05-31
author: H_On
description: 营业时重归硬件研究
tag: 树莓派
---
_第一次发布 2020/06/02_

### 目录
- [目录](#目录)
- [前情介绍](#前情介绍)
- [硬件设备](#硬件设备)
- [软件准备](#软件准备)
  - [系统](#系统)
  - [基础软件](#基础软件)
- [连线图](#连线图)
- [练习舵机的使用](#练习舵机的使用)
  - [连线](#连线)
  - [舵机测试运行代码](#舵机测试运行代码)
- [练习步进电机的使用](#练习步进电机的使用)
  - [首先还是连线](#首先还是连线)
  - [步进电机测试运行代码](#步进电机测试运行代码)
- [练习二路继电器的使用](#练习二路继电器的使用)
  - [首先还是连线](#首先还是连线-1)
  - [二路继电器测试运行代码](#二路继电器测试运行代码)
- [参考文章](#参考文章)

### 前情介绍
自从学校里的硬件研究不得志之后就很少再接触，只是偶尔兴趣使然又会自己研究研究，或许日后猹的回忆录会更新一个篇章。<br>
这次重拾硬件是因为营业，既然都是干活，软件也玩的有点腻了，换换口味觉得挺好~<br>
总之就是需要一个设备，可以模仿人手的操作 **滑动** 或是 **点击** 手机上的特定位置，暂时先做一个简单的，就是用点机滑动屏幕，舵机点击按钮。

### 硬件设备
与之前的 [远程温度监测系统](https://blog.zinchon.cn/2019/11/RemoteTemperatureMonitorSystem/) 相同
1. 一个自己管理的路由器（为了能够查看树莓派的 ip 方便之后 SSH），连接路由器的充电器和网线
2. 树莓派 3B RS英国版（当然这个型号只是猹的设备，现在已经有4代了大家可以用最新的）、树莓派的充电器和网线、装树莓派系统用的 SD 卡
3. 舵机两个，猹用的是这个 `MG996R` 型号的舵机
4. 两路继电器（2 Relay Module）一个
5. 杜邦线若干（其实三根母对母的就够了，后面会讲）
6. 学习用，在此项目中用不到，**可忽略**：步进电机+驱动板一套，猹用的是 `28BYJ-48-5V` 这个型号

### 软件准备
#### 系统
首先是系统，给树莓派装系统其实很简单，不会的同学参考猹的 [安装教程](https://blog.zinchon.cn/2019/12/RaspberrypiSystemInstall/) ，讲的很详细哒~<br>
顺便猹用的是 `raspbian` 系统，注意现在官方页面改版了，看清楚点击哪里哦

#### 基础软件
1. 首先更新一下系统中预装的软件
```sh
apt update; apt upgrade -y
```
2. 之后检查 `python` 和 `pip` 环境【猹习惯用 `python3`
```sh
python3 --version
# 如果没有 -> apt install python3
pip3 --version
# 如果没有 -> apt install python3-pip
```
3. 安装控制 io 口的函数库
```sh
pip3 install rpi.gpio
```
如果这个命令无法正常运行，请参考 [参考文章](#参考文章) 中的第三个链接中的其他方法

### 连线图
看过猹 de [基于树莓派的远程温度监测系统](https://blog.zinchon.cn/2019/11/RemoteTemperatureMonitorSystem/#%E7%A1%AC%E4%BB%B6%E8%BF%9E%E6%8E%A5) 这篇博客的话应该对这个图挺熟悉的，毕竟这个图是真的很实用<br>
![树莓派接口](/images/20200531/ioport.png)

### 练习舵机的使用
**如果是老司机请直接进入下一节**

#### 连线
连接舵机到树莓派上，[这里](https://tutorials-raspberrypi.com/raspberry-pi-servo-motor-control/)的示意图很清晰，猹这里为了方便用的是 17 口旁边的 GND 其实是一样的<br>
还有猹贴出来的教程里一开始说的是接 3.3V 的电源，猹失败了半天 ~~甚至把舵机拆开了~~ 才想到可能是电压不够，换 5V 的就可以了<br>
![演示图](/images/20200531/seline1.png)
![实际演示图](/images/20200531/seline2.jpg)

#### 舵机测试运行代码
快来用这个 **注释很清晰** 的代码来自己运行一下试试叭~
```py
#-*- coding:utf-8 -*-

import RPi.GPIO as rg
import time

if __name__ == '__main__':
    pin = 17                    # 预定义接口
    rg.setmode(rg.BCM)          # 设置 io 模式
    rg.setup(pin, rg.OUT)       # 设置指定端口为输出模式
    p = rg.PWM(pin, 50)         # 设置指定接口的 PWM 为 50Hz

    band = 10                   # 设置 pwm 值的范围宽度，此型号的范围是 2.5~12.5
    iniv = 2.5                  # 设置 pwm 的初始值，即范围的下限(2.5)
    pwmv = (0/180)*band + iniv  # 占空比计算公式，舵机通过占空比来控制角度，范围是 2.5~12.5 对应 0~180 度
    p.start(pwmv)               # 初始化数值为 2.5

    try:
        while True:
            for angle in range(0, 181):
                pwmv = (angle/180)*10 + 2.5
                p.ChangeDutyCycle(pwmv) # 转动到新的角度
                print('angle = %s  ' % angle, end = '\r')
                time.sleep(0.1)
            p.ChangeDutyCycle(2.5)
            time.sleep(2)
            # p.ChangeDutyCycle(2.5); time.sleep(1) # 可以只写这句代码来使舵机处于 0 度的状态，方便安装支架
    except KeyboardInterrupt:
        p.stop()
        rg.cleanup()
```

### 练习步进电机的使用
**如果是老司机请直接进入下一节**

#### 首先还是连线
如图所示步进电机直接接到驱动板上就行，参考 这里 的连线，按照自己的实际情况那些相同功能的 GPIO 都可以连，这里猹由于要和舵机一起使用，根据情况换了几个接口避免冲突<br>
![步进电机连线](/images/20200531/smline1.jpg)

#### 步进电机测试运行代码
快来用这个 **注释很清晰** 的代码来自己运行一下试试叭~
```py
#-*- coding:utf-8 -*-

import RPi.GPIO as rg
import time

if __name__ == '__main__':
    rg.setmode(rg.BCM)          # 设置 io 模式
    pins= [18, 27, 22, 16]      # 预定义接口
    for pin in pins:
        rg.setup(pin, rg.OUT)   # 设置指定端口为输出模式
        rg.output(pin, False)   # 初始化输出低电平【True表示输出高电平

    act = [
            [1, 0, 0, 1],
            [1, 0, 0, 0],
            [1, 1, 0, 0],
            [0, 1, 0, 0],
            [0, 1, 1, 0],
            [0, 0, 1, 0],
            [0, 0, 1, 1],
            [0, 0, 0, 1],
            ]

    for i in range(1 << 10):                    # 运行一段时间
        for s in range(len(act)):               # 循环每一个命令
            for p in range(4):                  # 循环每一个相位
                rg.output(pins[p], act[s][p])   # 设置对应相位输出对应的电平
            time.sleep(0.001)                   # 最短的时延就是 0.001 ，不能更小，会出错

    rg.cleanup()
```

### 练习二路继电器的使用
**如果是老司机请直接进入下一节**<br>
猹用的这个继电器长这样<br>
![继电器外貌](/images/20200531/relay1.jpg)<br>
如果是八路输入的继电器可以自行百度，那种很容易找到教程~

#### 首先还是连线
这个继电器它是控制了输出端的两个口之间的开关，所以输入的电源是需要接在输出的那个端口的<br>
借用参考文章里的视频中的截图理解一下<br>
![继电器输出](/images/20200531/relay2.png)

请看上面猹的示意图
> 1. 紫， 红， 橙三根线连接树莓派的 +5V 电源（树莓派的电源不够的话请自行焊接到一起
> 2. 黑线连接树莓派的 GND 脚
> 3. 绿线/黄线 连接对应元件的正极，原件的负极 **直接** 连接到树莓派的 GND 上即可
> 4. 白线/灰线 连接树莓派的 i/o 口，用来控制通断对应的输出（低电平有效

#### 二路继电器测试运行代码
快来用这个 **注释很清晰** 的代码来自己运行一下试试叭~
```py
#-*- coding:utf-8 -*-

import RPi.GPIO as rg
import time

if __name__ == '__main__':
    rg.setmode(rg.BCM)      # 设置 io 模式
    rep = 22                # 设置接口
    rg.setup(rep, rg.OUT)   # 设置接口为输出模式
    rg.output(rep, True)    # 设置输出高电平，即1口输出断路

    try:
        while True:
            # 接通 3s 后断开 3s
            rg.output(rep1, False)
            time.sleep(3)
            rg.output(rep1, True)
            time.sleep(3)
    except:
        # 退出时关闭输出并清空接口占用
        rg.output(rep1, True)
        time.sleep(3)
        rg.cleanup()
```
![继电器演示](/images/20200531/relay3.gif)

### 参考文章
[Install RPi.GPIO Python Library](https://www.raspberrypi-spy.co.uk/2012/05/install-rpi-gpio-python-library/)
1. 舵机
   * [树莓派PWM控制舵机的两种方式](https://www.basemu.com/rpi-control-servo-use-pwm.html)
   * [Raspberry Pi Servo Motor control](https://tutorials-raspberrypi.com/raspberry-pi-servo-motor-control/)
2. 步进电机
   * [How to connect Stepper motors to a Raspberry Pi](http://www.scraptopower.co.uk/Raspberry-Pi/how-to-connect-stepper-motors-a-raspberry-pi)
   * [Stepper Motor Control In Python](https://www.raspberrypi-spy.co.uk/2012/07/stepper-motor-control-in-python/)
   * [Controlling Stepper Motors using Python with a Raspberry Pi](https://medium.com/@Keithweaver_/controlling-stepper-motors-using-python-with-a-raspberry-pi-b3fbd482f886)
3. 继电器
   * [Control High Voltage Devices – Arduino Relay Tutorial](https://www.youtube.com/watch?v=LLFQ8sBWc80)
