---
layout: post
title: 用 STM32 最小系统板进行一些开发吧
date: 2020-09-11
author: H_On
description: STM32初始
tag: STM32
---

### 目录
- [目录](#目录)
- [前言](#前言)
- [硬件准备](#硬件准备)
- [硬件连接](#硬件连接)
- [软件准备](#软件准备)
- [驱动踩坑【PL2303HXA 自 2012 已停产，请联系供货商】解决方法](#驱动踩坑pl2303hxa-自-2012-已停产请联系供货商解决方法)
- [建立一个新项目](#建立一个新项目)
- [编写一个闪烁小灯的程序](#编写一个闪烁小灯的程序)
- [编译和烧录](#编译和烧录)
- [参考文章和视频](#参考文章和视频)

### 前言
这篇文章是为了记录猹首次进行基于 **STM32 最小系统板** 进行开发的经历和踩坑。

### 硬件准备
* stm32最小系统板【猹的型号是 `STM32F103C8T6`
* usb转ttl小工具【这个东西有很多种，猹这里介绍一直在用的一种
* 乱七八糟的零件，小 led 灯，杜邦线，按钮等

### 硬件连接
从51时代就开始用的 `usb 转 ttl` 工具很好用，在STM32上应该用3.3V只此区别<br>
上面有5个引脚，分别是

|:-:|:-:|:-:|:-:|:-:|
|3.3v|5v|TXD|RXD|GND|

**如图接线**<br>
![接线方法](/images/20200911/usb-ttl-stm32.png)

**实物接线图，大家按照颜色对应连线即可**<br>
![usb转ttl接线](/images/20200911/usb-ttl-b.jpg)
![usb转ttl接线](/images/20200911/usb-ttl-f.jpg)
大家这里注意一下，最小系统板上有两个跳线帽，把上面那个，旁边标着 `BOOT0` 的跳线帽接到右边，就是将中间的引脚与右边的 `1` 相连，这样一会烧录程序的时候，`reset` 才能进入烧录状态
![stm32最小系统板](/images/20200911/stm32-f.jpg)
![stm32最小系统板](/images/20200911/stm32-b.jpg)

### 软件准备
* Keil5 - 用于编写和编译单片机程序
* STM32器件包 - keil 原版是不支持编译 stm32 程序的
* mcuisp - 用于往单片机上烧录程序

### 驱动踩坑【PL2303HXA 自 2012 已停产，请联系供货商】解决方法
1. 一开始我连不上电脑，有文章说是要安装 `CH340` 驱动，才能通过usb转串口连接电脑
   > 然而这个驱动只能给开发板用，51 的开发板可以用，arduino 的板子可以用，就我的 stm32 最小系统板通过 usb 转 ttl 连接电脑不能用，最后一气之下搜索了我这边现实的奇怪串口 `PL2303HXA 自 2012 已停产，请联系供货商`
2. 百度后发现，原来是驱动版本需要降一个版本，这里只需要下载 `PL2303_Prolific_GPS_1013_20090319.zip` 解压出来以后运行安装一下即可在 `右键“此电脑”->属性->设备管理器->串口` 插上最小系统板后会看到新添加一个串口，没有的话刷新看看 `右键对应端口->浏览计算机以查找驱动程序软件->从计算机的设备驱动程序列表中选取->选择 3.3.2.105 的版本，双击即可`
   > 驱动的下载连接在下面最后一条 [参考文章](#参考文章和视频) 中有

第一个方法可能对有的同学有用哈，但是如果遇到猹的情况试试第二种~

### 建立一个新项目
1. 安装并注册keil5，具体操作视频比较直观，请看[这个视频](https://www.bilibili.com/video/BV1i7411679X?p=2)
2. 新建一个工程
   1. 点击 `工程->新建工程`<br>![新建工程](/images/20200911/newprojectstep1.png)
   2. 选中一个空文件夹（建议新建），然后写入工程名，最后点击 “保存”<br>![选择目录](/images/20200911/newprojectstep2.png)
   3. 选择要解释的单片机，先在搜索栏里输入咱们的型号，选中对应的型号之后点确定，一个工程就创建成功了<br>![选择型号](/images/20200911/newprojectstep3.png)
   4. 之后选择运行库，这里图示每个项目都必要的库，选中然后点击最下面的OK即可<br>![包](/images/20200911/basicpackage1.png) ![bao](/images/20200911/basicpackage2.png)
   5. 添加一个主程序，快捷键 `ctrl+n` 创建一个新文件，然后 `ctrl+s` 保存文件，选一个地址，建议跟工程文件放一起，然后在 keil 里右键程序文件夹，添加一个新文件<br>![添加主程序](/images/20200911/newprojectstep4.png)<br>
   `选中刚刚新建的 main.c 文件->添加->关闭` 这样就成功的吧主程序加入项目了<br>![选择文件](/images/20200911/newprojectstep5.png)

### 编写一个闪烁小灯的程序
让最小系统板上的测试灯 `PC13` 闪烁
> 这里先说一下，由于操作 io 口输出需要添加 `gpio库` 所以我们要新添加一个库文件，选中之后ok即可<br>![gpio库](/images/20200911/add-gpio.png)

```c
#include "stm32f10x.h"
#include "stdint.h"
#include "stm32f10x_rcc.h"
#include "stm32f10x_gpio.h"
#include "misc.h"

void delay_init(void) {
	//设置 SysTick 时钟为系统时钟(72MHZ)的8分频(9MHZ)，即以此计数耗时 1/9um
	SysTick_CLKSourceConfig(SysTick_CLKSource_HCLK_Div8);
}

void delay(uint32_t n) {
	uint32_t t;
	//关闭 SysTick 定时器
	SysTick->CTRL = 0x00;
	//延时重装载值
	SysTick->LOAD = n*(uint16_t)(SystemCoreClock/8000000)*1000;
	//清空计数器
	SysTick->VAL = 0x00;
	//启动计时器
	SysTick->CTRL |= 0x01;
	//等待延时结束
	do {t = SysTick->CTRL;} while ((t&0x01)&&!(t&(1<<16)));
	//关闭定时器
	SysTick->CTRL = 0x00;
	//清空计数器
	SysTick->VAL = 0x00;
}

void led_init(void) {
    //定义使用引脚结构体
	GPIO_InitTypeDef GPIO_InitStruct;
	//使能接口
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);
	//初始化引脚
	GPIO_InitStruct.GPIO_Mode=GPIO_Mode_Out_PP;
	GPIO_InitStruct.GPIO_Pin=GPIO_Pin_13;
	GPIO_InitStruct.GPIO_Speed=GPIO_Speed_50MHz;
	GPIO_Init(GPIOC, &GPIO_InitStruct);
	//设为高电平输出
	GPIO_SetBits(GPIOC, GPIO_Pin_13);
}

int main(void) {
    //初始化设置
	led_init();
	while (1) {
        //设置引脚输出低电平（点灯）
		GPIO_ResetBits(GPIOC, GPIO_Pin_13);
		delay(500);
        //设置引脚输出高电平（灭灯）
		GPIO_SetBits(GPIOC, GPIO_Pin_13);
		delay(500);
	}
}
```

### 编译和烧录
先在 keil 打开输出 hex 文件，`点击的魔法棒->选择输出(output)->将生成 hex 文件(Creat HEX File)打勾` 在进行编译即可（编译就是点最左边那三个按钮，不会有人不知道吧【雾<br>
![生成hex文件](/images/20200911/debug.png)

接下来是将生成的 hex 文件烧录进我们的 stm32 单片机，刚刚生成的 hex 文件就在工程根目录下的 `Object` 文件夹内，所以首先打开 `mcuisp.exe`，接下来：
1. 选择 STMISP
2. 选择好刚刚的 hex 文件
3. 选中编程前重装文件
4. 查看端口与设备管理器中的端口是否一致，如果没有就尝试点击 `搜索串口`，如果设备管理器里也没有或者不是 `CH340 驱动 or Prolific USB-to-Serial Comm Port`串口的话，请尝试上面的 [驱动踩坑](#驱动踩坑pl2303hxa-自-2012-已停产请联系供货商解决方法) 中的两种解决方法
5. 后面的频率是 `115200`
6. 下方的控制选项选择 `DTR 的低电平复位，RTS 高电平进 BootLoader`
7. 最后点击开始编程即可，如果单片机已经运行需要 `reset` 一下，记得上面说过的[接线方法](#硬件连接)，不要接错了，跳线帽一定要接到 `1` 上

![烧录](/images/20200911/record.png)

小灯闪起来辣~哦耶

### 参考文章和视频
* [小马哥 STM32F103 最小系统板开发课程视频](https://www.bilibili.com/video/BV1i7411679X?t=556)
* [STM32 最小系统串口下载](https://www.bilibili.com/video/BV1ee411x7Ns?t=7)
* [解决 "PL2303HXA 自 2012 已停产，请联系供货商" USB 不识别的问题](https://blog.csdn.net/wtf3505/article/details/104138727)
