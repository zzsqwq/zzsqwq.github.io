---
title: "利用树莓派为HP LaserJet 1020配置无线打印功能"
categories: [ "杂学","Ubuntu" ]
tags: [ "树莓派","HP LaserJet","打印机" ]
draft: false
slug: "198"
date: "2021-07-18 16:03:00"
---

## 前言

最近基地的打印机突然又好起来了。

因为基地的打印机型号比较老——HP LaserJet 1020，没有无线打印的功能。所以之前一位学长[^1] 用树莓派配置了打印机的无线打印功能，但是后来发现有一些问题，有时候发送打印请求树莓派无法接收，而且不知道为何，学长之前用的是树莓派自己创建WiFi，连接对应WiFi才能打印，但是这个显然不是最优的解决办法。

考虑到之间已经配置好基地WiFi，我决定重新配置一下打印功能，使其连接基地WiFi即可实现局域网打印。



---



## 配置过程

### 查看树莓派内容

通过ssh连接树莓派，发现里面除了Github上的一个开源项目[create_ap](https://github.com/oblique/create_ap) ，就没有什么其他的内容了，连接屏幕后发现没有任何图像信号，无从下手，因此考虑重新刷机。



---



### 对树莓派进行刷机

把数据备份了一下，看了一下树莓派版本是2015年生产的 `Raspberry Pi 3 model B V1.2` ，是老古董了。

去官网看了一下，因为我对Ubuntu比较熟悉，我计划安装一个 Ubuntu20.04版本的，考虑到版本比较老，就装了**server**版本的，相比与desktop版本负担更小一些。

其实就是下一个官方的软件，`Raspberry Pi Imager` ，直接用读卡器对树莓派的存储卡刷机即可。

这里是对应的镜像以及教程： [镜像下载](https://ubuntu.com/download/raspberry-pi)   [安装教程](https://ubuntu.com/tutorials/how-to-install-ubuntu-desktop-on-raspberry-pi-4#1-overview)



---



### 配置网络相关

Ubuntu的server版本有个比较蛋疼的问题就是上网比较困难，如果是用的学校网线，必须要PPPOE拨号才能上网，但是server版本居然没有 `net-tools` 和 `network-manager` ，连接WiFi啥的试了很多办法但还是没有什么作用。

**解决办法：用网线直接连接树莓派和有网的路由器，安装 `net-tools` 和 `network-manager` ，执行**

```shell
$ sudo nmtui
```

选择 `Activate a connect ` 连接无线的WiFi，执行

```shell
$ sudo ifconfig
```

查看WiFi对应的IP，至此，树莓派可以摆脱屏幕，我们可以使用电脑进行使用 `ssh` 连接。

这里也可以使用网线进行连接，具体操作如下

1. 用网线连接树莓派和自己的电脑。

2. 在树莓派的利用 `nmtui` 选择 `Edit a connection ` ，Add一个Ethernet connect，对IPv4 CONFIGURATION进行设置，首先讲 `Automatic` 设置为 `Manual`，设置 `Address` 为 **静态IP** 如 `192.168.3.2` ，`Gateway` 设置为 `192.168.3.1` 。

3. 在自己电脑利用 `nmtui` 同上不过设置 `Address` 为 `192.168.3.3` ，只要是位于同一网段即可。

4. 这时候就可以通过网线进行 `ssh` 连接了。



---



### 配置cups

以下大多参考：[如何正确地用树莓派共享打印机](https://sspai.com/post/40997)

大佬言：

> 其实，这一步的工作量非常少，因为软件包 CUPS 就是为共享打印机而生。我们要做的只是将打印机用 USB 线缆连接树莓派，然后安装并配置 CUPS。

然而，事实并非如此。

#### 换源

在安装之前需要换源，如果不换源的话，安装会十分缓慢，具体的流程可以看上面的blog，因为我们基地的WiFi自带代理，因此这一步我没有做。

#### 安装驱动及打印程序

+ 首先更新源并安装Hp的打印机驱动 `hplip`

```shell
$ sudo apt update
$ sudo apt install hplip
```

+ 然后安装Apple开源的远程打印工具 `cups`，并配置相应权限

```shell
$ sudo apt install cups  # Install cups package
$ sudo usermod -aG lpadmin pi # Add user to lpadmin group,pi is your user name
$ sudo cupsctl --remote-any # open remote access 
```

+ 然后使用在同一局域网的电脑，访问 `https://树莓派IP:631`，可以进入如下界面，按照下图设置右侧的`Server Settings`

 ![cups管理页面][1]

+ **连接打印机和树莓派**，点击Add Printer添加打印机，在弹出的窗口中输入对应的用户信息，使用在上一步中用户组中添加的用户

  ![cups登录界面][2]

+ 然后按着一步步的指引，选择HP LaserJet 1020打印机，然后按照提示，选择和名称对应的驱动，我们会发现 **LaserJet 1020** 对应驱动会提示：HP laserjet requires proprietary plugin，也就是我们不仅需要这个通用的驱动，而且需要一些额外的插件，打印机才能正常工作。
+ 查阅资料发现：HP官方已经给出了[说明](https://developers.hp.com/hp-linux-imaging-and-printing/binary_plugin.html)，我们需要查看hplip版本，然后安装对应的驱动插件。

  + 首先我们查看一下版本，发现是3.20.3版本

  ```shell
  $ sudo apt show hplip 
  Package: hplip
  Version: 3.20.3+dfsg0-2
  Priority: optional
  Section: utils
  Origin: Ubuntu
  Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
  Original-Maintainer: Debian Printing Team <debian-printing@lists.debian.org>
  Bugs: https://bugs.launchpad.net/ubuntu/+filebug
  Installed-Size: 518 kB
  ```

  + 然后在 [插件列表](https://developers.hp.com/hp-linux-imaging-and-printing/plugins) 中找到 3.20.3 对应的`hplip-3.20.3-plugin.run`和`hplip-3.20.3-plugin.run.asc`，使用wget下载到树莓派本地。

  + 运行 `hp-setup -i` 使用命令行进行安装，按照提示命令，进行插件的安装。

    [scode type="yellow"]这里建议提前下好安装，而不是直接联网下载，速度较快，指定路径需要为绝对路径[/scode]

+ 至此，我们可以尝试使用手机或者电脑搜索打印机来进行打印测试，没有其他意外的话，可以发现打印成功！



---



## 结语

其实这个配置过程远没有这么简单，期间还有很多小问题，但是大致的流程大概就是如上述所示，全写出来可能太啰嗦，大家如果配置过程中遇到更多的疑问，可以在下方评论一起探讨~



## 参考链接

+ [What is the HPLIP Binary Plug-In and How Do I Install It?](https://developers.hp.com/hp-linux-imaging-and-printing/binary_plugin.html)
+ [Plugins](https://developers.hp.com/hp-linux-imaging-and-printing/plugins)
+ [Other Plugins](https://www.openprinting.org/download/printdriver/auxfiles/HP/plugins/)
+ [cups](https://github.com/apple/cups)
+ [使用树莓派搭建无线打印机](https://www.jianshu.com/p/d3752c584e01)
+ [如何正确地用树莓派共享打印机](https://sspai.com/post/40997)



[^1]:学长的博客在这里:	[dykai](https://tec.blog.ykai.top/)


  [1]: https://blog.zzsqwq.cn/usr/uploads/2021/07/2334607761.png
  [2]: https://blog.zzsqwq.cn/usr/uploads/2021/07/3556322742.png