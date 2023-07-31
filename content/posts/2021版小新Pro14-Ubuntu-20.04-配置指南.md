---
title: "2021版小新Pro14 Ubuntu 20.04 配置指南"
categories: [ "Ubuntu" ]
tags: [ "ubuntu","小新Pro14","锐炬","Iris" ]
draft: false
slug: "215"
date: "2021-11-02 01:16:00"
---

# 2021版小新Pro14 Ubuntu 20.04 配置指南

## 补充

最近重装了 Ubuntu 20.04，又找了相关的一些帖子，发现 [聯想Yoga 14s 2021款裝機小記](https://blog.ryey.icu/lenovo-yoga-14s-2021.html#toc-entry-3) 中提到了下文中提到的**屏幕闪烁的问题**，解决办法是：只需要在内核参数中加入 `i915.enable_psr=0` 即可。

具体操作步骤如下：

```bash
$ sudo vim /etc/default/grub
```

在 GRUB_CMDLINE_LINUX_DEFAULT 这一行的最后添加 `i915.enable_psr=0`，保存后终端运行：

```bash
$ sudo update-grub
```

然后重启即可。

---

## 前言

苦于沉重游戏本的迫害，新买了一台小新Pro14 2021款，上手感觉还挺不错的。如下是配置：

+ CPU：酷睿 i5-11300H
+ 显卡：集成显卡 Intel 锐炬Iris Xe
+ 内存：16G
+ 外存：512 SSD
+ 屏幕：分辨率 2880x1800、400nits、100%sRGB

![电脑配置信息][1]

这里需要注意的是，不同时间出的小新Pro14配置是不太一样的，所以我这里列了一下配置。主要区别在于有一部分是2.2K分辨率+MX450显卡，而我这个是2.8K分辨率+锐炬Iris Xe显卡。

为了工作的需要，要装一个Ubuntu，先是装了之前用过的 Ubuntu 18.04，安装后发现触摸板无法使用，一系列探索后无果，在朋友的推荐下，还是决定安装 Ubuntu 20.04 试一下，踩了一些坑，在这里记录一下。

## 问题列表

如果你遇到了以下问题，那么这篇文章的方法可能会对你有益处：

### Ubuntu 18.04 相关

1. Ubuntu 18.04 无法使用触摸板
2. Ubuntu 18.04 无法使用内置键盘
3. Ubuntu 18.04 无法调节亮
4. Ubuntu 18.04 查看GPU发现是llvm，而不是Iris Xe

### Ubuntu 20.04 相关

1. Ubuntu 20.04 进入后屏幕花屏、黑屏
2. Ubuntu 20.04 查看GPU发现是llvm，而不是Iris Xe

## 现在达成的效果

Ubuntu 20.04 能够正常使用，触摸板以及外界屏幕，亮度调节均无问题。

开机时也能够使用键盘。

但是开机登录界面还是存在花屏、闪屏的问题，我通过自动登录解决。BIOS界面仍旧会闪屏。

## 安装 Ubuntu 18.04 的问题

首先安装Ubuntu 18.04 还是比较顺利，没有什么坑。

安装的话就是按流程来一遍——压缩卷、进入BIOS关闭安全启动模式`(这里网上有部分同学说也需要关闭 Intel Platform Trust Technology 、但是我不关闭也是可以的)`、然后Try Ubuntu看一下效果`（这里Try Ubuntu我没法使用鼠标，不过安装的时候可以）`、开始安装。

安装过程一般没啥问题，进入系统后我们就会发现一些问题，首先是你的**触摸板用不了，然后键盘也用不了。**再就是屏幕没法调节亮度（这个是小问题我觉得，好像也可以通过安装插件解决，大家可以自行搜索。）

经过查询资料，这里有同学已经提出很好的解决方案：https://zhuanlan.zhihu.com/p/322377515

简而言之，键盘用不了需要在 grub 启动项中加入 i8042.dumbkbd 参数，然后运行 

```shell
sudo update-grub2
```

即可在每次启动后保证键盘可用。

关于没法使用触摸板和调节亮度，办法就是升级内核，据说是内核升级到5.9.8以上可用，Ubuntu 18.04内置版本是5.4.0.84好像是，但是我在更新后会花屏、黑屏等来回鬼畜，试了好多四五个内核依旧不管用，我猜想是因为我是2.8K的屏幕而网上的教程多是基于2.2K屏幕的，锐炬显卡对于高分辨率的屏幕支持并没有那么优秀。

于是一直被这个问题折磨，搜了很多的教程也没有解决办法，最后决定换Ubuntu 20.04 尝试一下，之前一直觉得系统版本是个不可逾越的鸿沟，但是随着实践的越来越多，发现很多版本不兼容的问题都是有可解决办法的，因此也下定决心尝试一下未曾试过的 20.04。

## 安装Ubuntu 20.04 的问题

首先是关于安装的问题

在安装Ubuntu 18.04 的时候只有四个选项，应该是一个 Try Ubuntu、一个直接安装，一个高级模式，一个进BIOS

而安装Ubuntu 20.04 的时候却有五个选项，分别是Ubuntu、Ubuntu`(safe graphics)`、OEM install`(for manufacturers)`、还有就是一个是高级模式、一个是进BIOS

说一下三个安装方式的区别

第一个模式与第二个模式的区别就是，第二个模式对于grub启动项目中添加了一个 `nomodeset`  选项，那么这个选项是做什么的？以下是他的解释：

> The newest kernels have moved the video mode setting into the kernel. So all the programming of the hardware specific clock rates and registers on the video card happen in the kernel rather than in the X driver when the X server starts.. This makes it possible to have high resolution nice looking splash (boot) screens and flicker free transitions from boot splash to login screen. Unfortunately, on some cards this doesn’t work properly and you end up with a black screen. Adding the nomodeset parameter instructs the kernel to not load video drivers and use BIOS modes instead until X is loaded.

大概意思是在最新的内核中，已经能够在BIOS引导阶段启用显卡，这样做的目的是很好的适应高分辨率屏，但是很遗憾的是某些显卡并不能很好的适配，通过 `nomodeset` 参数可以防止以不支持的显卡驱动视频流。

很遗憾，锐炬显卡刚好没有被适配，所以选第一个选项`(Ubuntu)`来安装也会屏幕一闪一闪的，因此我们安装选择Ubuntu`(safe graphics)`选项来进行安装然后流程是一样的，蛮顺利。

这里装完就没有触摸板的问题，屏幕亮度调节也没有问题。不过键盘依旧有问题，可以根据上面描述进行更改。

然后更鬼畜的问题来了，只要这么一搞，从BIOS引导阶段开始，就会一直闪屏，尤其是输入密码进行登录的时候，会卡个好长时间，几乎无法使用，不过很有意思的是，只要外接屏幕，外接的屏幕显示不会有问题。

因此求助于搜索引擎，因为怀疑是显卡的问题，所以搜索了关键词 `Ubuntu Iris Xe`，找到了以下两个比较有用的答案：

StackOverflow：[Ubuntu 20.04 no driver loaded for Intel Iris Xe Graphics](https://askubuntu.com/questions/1299067/ubuntu-20-04-no-driver-loaded-for-intel-iris-xe-graphics)

Intel：[Intel Iris Xe MAX Graphics with Linux](https://dgpu-docs.intel.com/devices/iris-xe-max-graphics/index.html)

这两个帖子都说了一个问题吧，就是如何在 Ubuntu 20.04 上更好的使用锐炬显卡

这个问题实质解决的是没有在Ubuntu 20.04 上启动起来显卡，所以你会在你的 Ubuntu->Settings->About 页面看到的是 llvm 有关的字眼，而不是上面我截图所示的 `Mesa Intel® Xe Graphics (TGL GT2)`。

解决这个问题比较关键的步骤是

```shell
sudo apt update
sudo apt install linux-oem-20.04
sudo reboot
```

这样开机再启动应该就会正常启动显卡了，这个方式在Ubuntu18.04是否奏效我没有实验过，可能可以安装对应的 `linux-oem-18.04`  包。

在 Intel 官方的教程中，还添加了 grub 启动项等，我并没有发现他们的实际作用，在他后续的测试中我也没有达到期待的效果，因此没有继续尝试，如果有同学跟着文档做成功了，可以一起来讨论一下。

**但是！！！**

~~安装完成后，我还是会花屏和黑屏，问题依旧没有解决。我突发奇想，考虑到我外接屏幕没有问题，而自带的屏幕有问题，**因为外接屏是 1920x1080 而内置屏幕是 2880x1800，我联想到会不会是高分辨率屏幕的问题，所以尝试着把外接屏的显示比例调成了 150%`(需要开启 Fractional Scaling)`，没想到歪打正着，居然好了，看起来也更加的顺眼，比例也更加协调。**具体的内部原因还不是很清楚。~~  已解决，见本文开头。

![比例配置信息][2]

但是还是有一个小瑕疵，就是在BIOS引导阶段与输入密码登录的界面，我仍旧是会花屏、闪屏，我考虑到这是还没有初始化屏幕设置的问题，尝试搜索了修改BIOS比例、提前初始化login界面的分辨率，依旧没有找到比较好的解决办法，

因此最后只好启动自动登录来跳过登录界面，这样就**看上去算是一个完好的系统**了QAQ。。

这里给出一些可能有价值的资料，大家可以自行查阅

[How to change the login screen resolution in Ubuntu 18.04](https://askubuntu.com/questions/1041677/how-to-change-the-login-screen-resolution-in-ubuntu-18-04)

[Custom Resolution Ubuntu 20.04](https://askubuntu.com/questions/1252172/custom-resolution-ubuntu-20-04)

[How can I change the resolution of the GRUB menu?](https://askubuntu.com/questions/1091778/how-can-i-change-the-resolution-of-the-grub-menu)

根据上述第三个教程，我修改了 GRUB 的显示分辨率为 1920x1080，还是会闪屏。

如果有大佬有想法或者解决了，欢迎留言一起探讨。

## 后记与感想

折腾了一下午加一晚上，终于把系统整的能用了，不过又要重新配置各种软件，还是挺麻烦的。

不过尝试新鲜事物、好比Windows11、最新款的电脑，还是挺高血压的，需要应付各种Bug，这可能也是一种平衡？hhhh，想要尝试新的事物、走在前沿、就必须要有付出。

此外，发现查英文的资料要比中文靠谱的多，尤其是这种比较新的问题、要多去StackOverflow和AskUbuntu等论坛和官网查看，有奇效。

## 参考资料

1. [联想小新pro14安装Ubuntu20.04](https://zhuanlan.zhihu.com/p/322377515)
2. [Ubuntu 20.04 no driver loaded for Intel Iris Xe Graphics](https://askubuntu.com/questions/1299067/ubuntu-20-04-no-driver-loaded-for-intel-iris-xe-graphics)
3. [Intel Iris Xe MAX Graphics with Linux](https://dgpu-docs.intel.com/devices/iris-xe-max-graphics/index.html)
4. [How to change the login screen resolution in Ubuntu 18.04](https://askubuntu.com/questions/1041677/how-to-change-the-login-screen-resolution-in-ubuntu-18-04)
5. [Custom Resolution Ubuntu 20.04](https://askubuntu.com/questions/1252172/custom-resolution-ubuntu-20-04)
6. [How can I change the resolution of the GRUB menu?](https://askubuntu.com/questions/1091778/how-can-i-change-the-resolution-of-the-grub-menu)

[1]: https://blog.zzsqwq.cn/usr/uploads/2021/11/3138090261.png
[2]: https://blog.zzsqwq.cn/usr/uploads/2021/11/3113030601.png