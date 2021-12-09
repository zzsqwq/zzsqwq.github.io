---
title: "deepin-wine-qq-9.1.8版本无法正常启动的解决方案"
categories: [ "Ubuntu" ]
tags: [ "ubuntu","QQ","steam" ]
draft: false
slug: "195"
date: "2021-06-16 01:38:00"
---

### 问题描述

​	Ubuntu下想要使用QQ有一个比较好的解决方案就是deepin-wine的版本，deepin-wine版本的QQ一共有两个版本，分别是 8.9.1 和 9.1.8 ，前者安装后发现无法登陆，登录时会提示版本过低的问题，于是我换到9.1.8版本后，启动初始化后就无任何信息了，于是开始排查问题



### 解决方案

​	首先我们根据上文的启示，因为每一个应用程序对应了一个 `xxx.desktop` 文件，因此在应用库中的QQ一定也有一个对应的 `desktop` 文件

![QQ的启动方式][1]

​	我们进入到 `/usr/share/applications` ，运行 

```shell
$ ls | grep -i qq
```

​	可以发现其中有一个名为 `deepin.com.qq.im.desktop` 的文件，我们打开后发现内容如下：

```shell
#!/usr/bin/env xdg-open

[Desktop Entry]
Encoding=UTF-8
Type=Application
X-Created-By=Deepin WINE Team
Categories=chat;
Icon=deepin.com.qq.im
Exec="/opt/deepinwine/apps/Deepin-QQ/run.sh" -u %u
Name=QQ
Name[zh_CN]=QQ
Comment=Tencent QQ Client on Deepin Wine
StartupWMClass=QQ.exe
MimeType=
```

​	可以看到Exec那一栏为 `Exec="/opt/deepinwine/apps/Deepin-QQ/run.sh" -u %u` ，发现他是运行目录下的一个 `run.sh`  脚本来启动的。

​	我们进入目录下直接运行该脚本，查看log信息：

```shell
base ❯ ./run.sh                   
Run Deepin-QQ 9.1.8deepin0 c:/Program Files/Tencent/QQ/Bin/QQ.exe
run Deepin-QQ progress pid 
Gtk-Message: 01:16:58.069: GtkDialog mapped without a transient parent. This is discouraged.
total 0
lrwxrwxrwx 1 zs zs 10 6月  16 01:16 c: -> ../drive_c
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com1 -> /dev/ttyS0
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com10 -> /dev/ttyS9
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com11 -> /dev/ttyS10
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com12 -> /dev/ttyS11
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com13 -> /dev/ttyS12
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com14 -> /dev/ttyS13
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com15 -> /dev/ttyS14
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com16 -> /dev/ttyS15
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com17 -> /dev/ttyS16
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com18 -> /dev/ttyS17
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com19 -> /dev/ttyS18
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com2 -> /dev/ttyS1
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com20 -> /dev/ttyS19
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com21 -> /dev/ttyS20
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com22 -> /dev/ttyS21
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com23 -> /dev/ttyS22
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com24 -> /dev/ttyS23
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com25 -> /dev/ttyS24
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com26 -> /dev/ttyS25
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com27 -> /dev/ttyS26
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com28 -> /dev/ttyS27
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com29 -> /dev/ttyS28
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com3 -> /dev/ttyS2
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com30 -> /dev/ttyS29
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com31 -> /dev/ttyS30
lrwxrwxrwx 1 zs zs 11 6月  15 23:36 com32 -> /dev/ttyS31
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com4 -> /dev/ttyS3
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com5 -> /dev/ttyS4
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com6 -> /dev/ttyS5
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com7 -> /dev/ttyS6
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com8 -> /dev/ttyS7
lrwxrwxrwx 1 zs zs 10 6月  15 23:36 com9 -> /dev/ttyS8
lrwxrwxrwx 1 zs zs  8 6月  16 01:16 y: -> /home/zs
lrwxrwxrwx 1 zs zs  1 6月  16 01:16 z: -> /
CallApp Deepin-QQ c:/Program Files/Tencent/QQ/Bin/QQ.exe
2021年 06月 16日 星期三 01:16:58 CST:kill QQ.exe block
2021年 06月 16日 星期三 01:16:58 CST:No wine process found
/home/zs/.deepinwine/Deepin-QQ/drive_c/Program Files/Tencent/QQ/Bin
Starting process c:/Program Files/Tencent/QQ/Bin/QQ.exe ...

/opt/deepinwine/apps/Deepin-QQ
base ❯ wine: cannot find L"C:\\windows\\system32\\winemenubuilder.exe"
wine version: 2.18
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: swrast
X Error of failed request:  GLXBadContext
  Major opcode of failed request:  152 (GLX)
  Minor opcode of failed request:  6 (X_GLXIsDirect)
  Serial number of failed request:  207
  Current serial number in output stream:  206
```

​	可以发现最下面的log信息有一些异常，首先第一行是因为我们是Ubuntu系统，可以暂且不关注

​	接下来可以看到有一个LibGL的错误，我们通过Google搜索

```shell
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: swrast
X Error of failed request:  GLXBadContext
  Major opcode of failed request:  152 (GLX)
  Minor opcode of failed request:  6 (X_GLXIsDirect)
  Serial number of failed request:  207
  Current serial number in output stream:  206
```

​	发现类似的错误及解决方案如下：

+ [SOLVED\] LibGL errors with osu! and wine](https://bbs.archlinux.org/viewtopic.php?id=255385)

+ [Steam: libGL error: No matching fbConfigs or visuals found libGL error: failed to load driver: swrast](https://askubuntu.com/questions/834254/steam-libgl-error-no-matching-fbconfigs-or-visuals-found-libgl-error-failed-t)

​	从搜索结果来看，这个问题还是非常常见的，用steam也会遇到，一般都是因为电脑安装了64位的NVIDIA显卡驱动，但是因为应用是32位的导致不能兼容，因此无法启动

​	最终解决方案有两个

+ 如果是不常用NVIDIA驱动的人，普通的办公一下，可以搜索网上教程关闭独显，只启用集显，可以发现QQ可以正常启动，
+ 重新安装32位的NVIDIA驱动



### 最后

​	虽然已经知道了解决方案，但是最终我还是选择卸载掉QQ，安装了wine版本的TIM，感觉和QQ没什么区别，而且更加简洁，而且可以流畅运行，没有N卡兼容问题！大家可以考虑一下~

​	而且QQ和TIM某些情况下会出现bug，字体全部变为方块，在 deepin-wine-ubuntu 的 [Issues](https://github.com/wszqkzqk/deepin-wine-ubuntu/issues) 中找到了解决方案 [Ubuntu 安装QQ后中文方块解决方法](https://github.com/wszqkzqk/deepin-wine-ubuntu/issues/253) ，大家有同样困扰的也可以看一下~


  [1]: https://www.zzsqwq.cn/usr/uploads/2021/06/75817321.png