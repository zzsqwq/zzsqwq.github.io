---
title: "Ubuntu如何利用AppImage创建Qv2ray快捷方式"
categories: [ "杂学","Ubuntu" ]
tags: [ "ubuntu","AppImage" ]
draft: false
slug: "193"
date: "2021-06-16 00:54:00"
---

### 前言

最近因为之前的Ubuntu系统有一些环境和神奇的问题，把Ubuntu重装了，在配置Qv2ray的时候，之前一直是把AppImage放到桌面上直接使用，这样的话一直用起来不仅难找而且操作有点困难，因此考虑将他放到命令行启动并且可以在快捷方式中找到。效果如下图所示：


![快捷方式效果图][1]


### 原理

首先需要明白的是这个里面的每个快捷方式，其实都是解析一个文件得到的，这些文件都存在于 `\usr\share\applications` 中，文件名为 `**.desktop` ，随便打开里面的一个文件看一下，Typora的启动文件内容如下：

```shell
[Desktop Entry]
Name=Typora
Comment=a minimal Markdown reading & writing app. Change Log: (https://typora.io/windows/dev_release.html)
GenericName=Markdown Editor
Exec=typora %U
Icon=typora
Type=Application
StartupNotify=true
Categories=Office;WordProcessor;
MimeType=text/markdown;text/x-markdown;
```

可以看到里面的内容写的非常清晰，其中比较重要的有以下几个

| 关键词          | 意义                   |
| --------------- | ---------------------- |
| [Desktop Entry] | 文件头                 |
| Name            | 应用名称               |
| Comment         | 描述                   |
| Exec            | 执行的命令             |
| Icon            | 图标路径               |
| Type            | 启动器类型             |
| Categories      | 应用的类型（内容相关） |
| StartupNotify   | 启动时是否打印输出信息 |
| Terminal        | 是否在终端中运行       |



### 快捷方式的创建

明白以上原理，我们只需要创建一个 `qv2ray.desktop` ，文件内容如下：

```shell
[Desktop Entry]
Name=qv2ray
Exec=/home/zs/proxy/v2ray/Qv2ray.v2.6.3.linux-x64.AppImage
Icon=/home/zs/proxy/v2ray/qv2ray.512.png
Type=Application
StartupNotify=true
```

其中Exec为你AppImage的绝对路径，Icon为图标的绝对路径。

其中各个大小的Qv2ray的icons可以在它的Github中找到，链接为：[Qv2ray/icons](https://github.com/Qv2ray/Qv2ray/tree/master/assets/icons)

创建完后执行

```shell
$ sudo chmod +x qv2ray.desktop
```

为文件添加可执行权限，然后执行

```shell
$ sudo cp qv2ray.desktop /usr/share/applications
```

将其加入到目录中，接下来我们就可以在应用库中找到对应的qv2ray了~



### 命令行快捷方式的创建

我们直接将AppImage加入到 `/usr/bin` 中即可，即执行以下命令：

```shell
$ sudo cp xxx.AppImage /usr/bin/qv2ray
```

接下来我们在终端中输入 `qv2ray` ，发现已经可以成功打开qv2ray，大功告成！~


[1]: https://blog.zzsqwq.cn/usr/uploads/2021/06/2642879187.png
