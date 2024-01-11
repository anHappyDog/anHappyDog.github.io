---
title: jetson nano b01 使用串口进行初始化
author: lonelywatch
date: 2022-09-5 15:14
categories: [BOARD]
tags: [BOARD]
---

jetson nano b01在初始化系统是默认开启ssh的（在这之前ssh 192.168.55.1会被refused），如果只有一根5V -2A的电源线，可以连接电脑和jetson nano 的micro-usb接口，然后使用headless的方式通过串口来进行nano的初始化。

流程大概如下：

使用micro-usb线将nano与电源线连接，如果tf卡正常且硬件无问题的话，在win10中会有类似于网卡的RNDIS出现。

然后在win10中使用putty，然后选择Serial连接，设备根据电脑的设备管理器，查看USB串口是哪个，我这里是COM5，然后波特率选115200，然后连接，等待片刻（如果太久没出现就重启板子然后再次连接），就会出现nvinda的使用协议，一路填过去，配置完成后，串口就会断开，这个时候就可以使用ssh连接了（输入的话，输入Tab，进行切换）。

有个疑惑的点就是我在没初始化前连接hdmi也是黑屏无信号，真是奇怪。

如果是linux或者是mac也是利用类似的方式连接mac。

参考如下：[Jetson Nano Headless WiFi Setup – desertbot.io](https://desertbot.io/blog/jetson-nano-headless-wifi-setup)