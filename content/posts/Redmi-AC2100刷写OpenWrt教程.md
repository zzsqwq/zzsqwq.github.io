---
title: "Redmi AC2100刷写OpenWrt教程"
categories: [ "路由器" ]
tags: [ "路由器","RM2100","科学上网" ]
draft: false
slug: "209"
date: "2021-08-01 21:48:00"
---

# Redmi AC2100刷写Openwrt教程

## 前言

此教程的书写基于 Redmi AC2100 路由器，并不保证其他路由器可用。

推荐使用的 Host 主机为 Windows 10/11 系统，当然，Linux 也可以，不过某些地方用的软件有差异，但是用 Linux 的人肯定不简单，就不用我多做说明了。

理论上对其他型号路由器也通用，不过需要改一下固件的型号。本教程参考了多位前辈的文档与教程，其实虽说是教程，更像是一个整合，文章最后会给出过程中的参考链接，在此对他们一并感谢！



## 教程

### 一、刷写官方固件

#### 从官方固件刷入其他版本的官方固件

你可能会疑问，既然已经是官方固件了，为什么还需要刷写官方固件？

对于此教程，这一步可能非必要。但是如果你是基于 **telnet** 来对固件进行刷写，那么可能需要重刷官方固件，截止目前这个节点 **2021.8.1** 官方最新的固件会出现在开启 **telnet** 时卡 **shell** 的情况。

具体表现为无法在弹出的窗口中粘贴文本，并且不会有后续的反应。 这时候可以刷回官方原固件，常见比较稳定的有 `2.0.7`， `2.0.376`， `1.0.14` 啥的，这个大家可以自己尝试一下~ 不过，此教程不是基于 **telnet** 的，对于 **Redmi AC2100** 我也不推荐使用 **telnet** 这个方法，因为经过我的尝试发现，他的成功率比较低，而且异常繁琐！

**官方固件的刷写过程如下：**

> 1. 首先在 [此处](http://openwrt.ink:8666/%E5%AE%98%E6%96%B9%E5%B7%A5%E5%85%B7%E5%8C%85/) 下载修复工具包与ROM包
>
> 2. 然后接通路由器电源，用网线将路由器的 `LAN` 口与电脑连接。
> 3. 打开小米路由器修复工具，上传本地对应的刷机包。
> 4. 选择与路由器 `LAN` 口相连的网卡（这里如果不清楚选哪一个，建议在网络设置中把其他的网卡都禁用掉）
> 5. 然后断开路由器电源，用牙签或者其他的按住 reset 键后，接通电源，直到路由器橙灯闪烁时松开 reset 键。
> 6. 等待到蓝灯闪烁时，即可访问网关或者 `miwifi.com` 来设置路由器，固件刷写成功。

#### 从Breed刷写官方固件

**Breed** 是一个由 [hackpascal](https://www.right.com.cn/forum/space-uid-200302.html) 大神自写的与 **U-Boot** 同级的 Bootloader。

每次我们刷写固件不会刷写此部分，我们通过 `Reset` 键重置后，此部分不会被重置。像下面（这里因为我已经刷好了固件，不想 Reset 掉，因此用的其他大佬的截图，因此这里的型号不是 **Redmi AC2100**）：

![Breed的首页][1]

可以看到上图有一个固件更新部分，点开后详细信息如下：

![固件更新部分图示][2]

可以看到上图有包含三个部分的刷写，Bootloader、固件、EEPROM这三个部分，我们如果想要刷会官方的这两部分都要刷。

平时我们说的 openwrt，padavan 等都是固件部分，breed 是 Bootloader 部分。我们如果要完全刷回官方的部件，需要在这里的 Bootloader 部分刷写官方的 Bootloader，然后按照上述官方的方法来刷写整体后面的固件！**我测试时如果 Bootloader 部分是 breed，那么无法直接通过上面的方法进行重置。**官方的 Bootloader 可以在下方链接找到：

[红米 AC2100 官方 Bootloader](http://openwrt.ink:88/upload/2020/12/bootloader-redmi-ac2100-928443369e734b72b9611377b94598bb.bin)        

[小米 AC2100 官方 Bootloader](http://openwrt.ink:88/upload/2020/12/bootloader-xiaomi-ac2100-2ad34eb9357e4760948f797999b7e294.bin)



### 二、从官方部件刷写 Breed

#### 为什么需要刷 Breed？

我们注意到在 **Breed** 过程中，**固件更新** 部分也可以刷写固件，根据以往大家的经验来说，固件最好从 **Breed** 部分刷写还是最保险的。

此外，**Breed** 可以使你方便的更换其他的固件，因为 **Reset** 不会重置 **Breed** 而会重置固件，因此如果你想把 **Openwrt** 换成 **Padavan**，那么只需要 **Reset** 一下，在 **Breed** 重刷一下固件就可以了！



#### 刷写流程

##### 打开路由器 ssh

首先我们用网线将路由器 `LAN` 口与电脑连接

在 **Linux** 用 `ifconfig` 或者在 **Windows** 中使用 `ipconfig` 来查看路由器分发的 `ip` 。

好比如果是 `192.168.31.x ` 那么我们就访问 `192.168.31.1` ，或者可以尝试直接访问 `miwifi.com` ，会进入到设置路由器的管理页面，我们填写一系列配置，然后登录。

登录后，查看地址栏，一般地址如下，这里需要注意，我地址中的 `192.168.31.1 ` 是我访问的地址为此，如果你那里 `ip` 不是这样，那么将下面所有的 `192.168.31.1` 替换成你当中对应的 `ip`： 

```
http://192.168.31.1/cgi-bin/luci/;stok=075a9192918557c27cdbcae2175281d9/web/home#router
```

这里我们注意 `stok= ` 到 `/web` 这一段中间的码值，好比设其为 `KEY` 

```
KEY = 075a9192918557c27cdbcae2175281d9
```

然后我们在地址栏输入如下地址并回车，地址中 `${KEY}` 的意思是这里就填入上面 `KEY` 对应的数值：

```
http://192.168.31.1/cgi-bin/luci/;stok=${KEY}/api/misystem/set_config_iotdev?bssid=Xiaomi&user_id=longdike&ssid=-h%3B%20nvram%20set%20ssh_en%3D1%3B%20nvram%20commit%3B%20sed%20-i%20's%2Fchannel%3D.*%2Fchannel%3D%5C%22debug%5C%22%2Fg'%20%2Fetc%2Finit.d%2Fdropbear%3B%20%2Fetc%2Finit.d%2Fdropbear%20start%3B
```

这样就打开了路由器的 **ssh** ，我们接下来在地址栏打开：

```
http://192.168.31.1/cgi-bin/luci/;stok=${KEY}/api/misystem/set_config_iotdev?bssid=Xiaomi&user_id=longdike&ssid=-h%3B%20echo%20-e%20'admin%5Cnadmin'%20%7C%20passwd%20root%3B
```

这就将路由器的 **ssh** 密码设置为 `admin` 。

##### 刷写 breed

首先我们下载一下适用于 **Redmi AC2100** 的 **Breed**，[点此下载](https://breed.hackpascal.net/breed-mt7621-xiaomi-r3g.bin)

我们在 **Windows** 左下侧搜索框中搜索 `cmd` 然后打开。

输入

```shell
ssh root@192.168.31.1
```

然后输入 `admin` ，回车。 

不出意外的话，我们就成功使用 `ssh` 登录了路由器中的系统。

然后我们按 `Ctrl + D` 退出登录

输入

```shell
scp /path-to-breed root@192.168.31.1:/tmp
```

这里的 `/path-to-breed` 代表的是上面下载的 **Breed** 的路径，例如 `D:\Downloads\breed-mt7621-xiaomi-r3g.bin` ，这条命令即将下载的 **Breed** 传输到了路由器的 `/tmp` 目录中。

接下来我们按上述过程重新登录路由器后，输入

```shell
cd /tmp
```

进入路由器中的 `/tmp` 目录，接下来运行

```shell
mtd -r write breed-mt7621-xiaomi-r3g.bin Bootloader
```

这样我们就成功将 **Breed** 刷写到了路由器中！

我们断掉路由器电源，重启一下，访问 `192.168.1.1` 即可打开 **Breed** 控制台。



### 三、从 breed 刷写固件

首先是下载我们需要的固件，在后面我会给出一系列现阶段尝试过的固件，并给出说明，大家可以自己选择合适自己的固件。

这部分很简单，我们只需要下载我们想要刷写的固件，进入 breed 控制台，点击固件更新，在固件那部分点击后面的 **浏览** ，上传我们想要刷写的固件，点击 **上传** ，验证后会弹出新的窗口，点击 **刷写** 即可！

![在固件部分点击浏览选择固件后上传][3]

### 四、配置 Openwrt 固件

因为我自己在用 Openwrt ，所以这里只讲一下配置 Openwrt 的过程。

说明一下，这里我使用的是 `small_5` 大佬的 Openwrt 固件，其他的固件可能界面有些差别，但是其实大同小异，可以类比着来做。

我用的固件网关为 `10.0.0.1` ，这个一般都是固件作者自己设定的，比较简单地方式是将路由器 `LAN` 口与电脑连接，查看自己的 `ip` 地址，如果是 `10.0.0.x` 的话就是 `10.0.0.1` ，这里涉及到一个网关的概念，大家有兴趣的可以自行查找资料查看。 



#### 配置 IPv4 网络

首先将网线与路由器的 `WAN` 口连接。

现在网络无论是学校还是家里一般都是用的 `PPPOE` 拨号上网。

我们打开 **网络** 中的 **接口** ，然后编辑 `WAN` 口信息，将协议切换为 `PPPoE` ，填写用户名和密码，其他一般不用管，直接保存即可。

可选：这里可以选择编辑 `LAN` 口信息，这里涉及的是局域网的信息。

好比你现在 `ip` 是 `10.0.0.x` ，可以注意到协议为静态地址，然后 `IPv4` 地址为 `10.0.0.1/24` 这样的，我们如果是改成 `192.168.1.1` ，那么后续我们的地址一般就是形如 `192.168.1.x` 了。

因为局域网地址一般都是这样，我就也这么改了，不过这里改不改都行。**但是需要注意的是！我们如果改掉了，我们后续访问管理界面就不是 `10.0.0.1` 了，而是 `192.168.1.1` ！**



#### 配置科学上网

说实话，我学习刷机就是为了这个，估计大多数人也是。

不过现在 **Openwrt** 中科学上网插件的种类繁多，如 `Passwall` 、`SSR+` 、`Overwall` 等。这三个我都用过，用起来都差别不大，界面也是差不多的。此固件自带的 `Overwall` 如下：

![服务器节点订阅与管理部分][4]

我们只需要在 **订阅URL** 部分填入我们的订阅链接，然后依次点击 **更新订阅设置** 以及 **更新所有订阅服务器节点** ，然后可以发现下方出现了我们的节点，点击 **使用** ，然后 **保存及应用** ，就完成了。

这里可以在页面调节一下设置，好比可以设置 **仅常用端口走代理** 等。

#### 配置DDNS

##### 什么是DDNS?

首先说一下什么是动态DNS，又叫 DDNS。下面是来自维基百科的解释：

> **动态DNS**（英语：**Dynamic DNS**，简称**DDNS**）是[域名系统](https://zh.wikipedia.org/wiki/域名系统)（DNS）中的一种自动更新[名称服务器](https://zh.wikipedia.org/wiki/名稱伺服器)（Name server）内容的技术。根据互联网的域名订立规则，域名必须跟从固定的IP地址。但动态DNS系统为动态网域提供一个固定的[名称服务器](https://zh.wikipedia.org/wiki/名稱伺服器)（Name server），透过即时更新，使外界用户能够连上动态用户的网址。
>
> 这个术语被用来描述两种不同的概念。在[互联网](https://zh.wikipedia.org/wiki/網際網路)的管理层面来说，动态DNS更新是指创建一个DNS系统，能够自动更新传统的DNS记录，而不需要手动编辑。这个机制在RFC 2136中被解释，利用[TSIG](https://zh.wikipedia.org/w/index.php?title=TSIG&action=edit&redlink=1)机制来提供安全性。
>
> 在客户端来说，动态DNS提供了一个轻量化机制，让本地DNS数据库可以即时的更新。它能把[互联网](https://zh.wikipedia.org/wiki/互聯網)[域名](https://zh.wikipedia.org/wiki/域名)指往一个可能经常改变的[IP地址](https://zh.wikipedia.org/wiki/IP地址)，让经常改变位置及配置的设备，能够持续性的更新IP地址。令互联网上的外界用户可以透过一个大家知道的域名，连接到一个可能经常动态改变[IP地址](https://zh.wikipedia.org/wiki/IP地址)的机器。其中一个常用的用途是在使用动态IP地址连线（例如在每次接通连线就会被分配一个新的IP地址的拨号连线，或是偶尔会被ISP变更IP地址的DSL连线等）的电脑上运行[服务器](https://zh.wikipedia.org/wiki/伺服器)软件。
>
> 若要实现动态DNS，就需要将网域的“最大缓存时间”设置在一个非常短的时间（一般为数分钟）。此举可避免外界用户在缓存中保留了旧的IP地址，并且使每个新连线被创建时都会经过Name Server获取该机器的新地址。
>
> 各种机构都有大规模地提供动态DNS的服务。他们会利用[数据库](https://zh.wikipedia.org/wiki/数据库)存储用户当前的IP地址，并会对用户提供更新当前IP地址的方法。当一些"客户"程序被安装了之后，会在后台运行并每隔数分钟检查电脑的IP地址。当发现其IP地址有所变更，程序便会提交一个更新IP地址的请求至动态DNS的服务器。有很多[路由器](https://zh.wikipedia.org/wiki/路由器)和其他网络设备也在其[固件](https://zh.wikipedia.org/wiki/韌體)中包含了上述的功能。



举个例子：

好比我们一个公网 `ip` 对于外网可见，我们想要在公司设置一下路由器，但是我们不知道路由器的准确 `ip` 。那么我们可以通过一个域名解析为这个 `ip` ，好比域名为 `ddns.zzsqwq.cn` ，我们只需要访问 `ddns.zzsqwq.cn` 即可访问管理页面或者 `ssh` 过去对路由器更改。

但是这个公网 `ip` 可能随时是变的，我们不能绑定死，那么即可利用动态 DNS 来监视 `ip` 的变化，如果此 `ip` 变化了，我们即向供应商发送更新 `ip` 的请求，以保证我们每次访问 `ddns.zzsqwq.cn` 都可以解析到正确的 `ip` 。



##### 如何设置DDNS?

简单讲一下如何配置我所用固件的 **ddns**，可能对其他固件并不通用，仅供参考！

大多数的 **Openwrt** 固件中都带了 **ddns**，我这里用的固件里面 **ddns** 做了一个集成，如下图所示：

![DDNS首页图示][5]

我们首先需要注册一个域名，大家如果有做博客或者搭建服务器啥的，应该都有域名。

没有的话可以尝试去阿里云或者腾讯云买一个，我这里是在腾讯云买的，因此使用的是腾讯的 `dndpod` 的解析服务，首先在 `dnspod` 中注册一个帐号，点击右上角头像，选择 `API密钥` 。

![选择API密钥][6]

 选择 `DNSPod Token` ，点击 **创建密钥**。

![创建密钥][7]

然后记下密钥的 `ID` 与 `Token` ，在 ddns 设置页面选择编辑对应的 ddns 服务，或者新建一个。

![IPv4 DDNS设置图示][8]

如上图所示，首先选择 DDNS 服务商，如 `aliyun` 、`dnspod` 等，然后查询主机名与域名如上图所示填写，注意域名那里要用 `@` ，这可能是这个插件的特性，用其他固件的插件可能是 `.` 。

然后用户名与密码处填写刚才生成密钥的 `ID` 与 `Token` ，保存即可。

设置 **IPv6** 的 **ddns** 同理，设置完成后重启一下 **ddns** 才会生效。



#### 解析内网IP

之前配置完路由器，总是无法访问我们基地主机的服务。

后来查询了一下，才发现因为基地主机是内网 `ip` ，Openwrt 一般用的 **dns** 解析服务为 `dnsmasq` ，他会有一个 `重绑定保护` ，这个是为了防止 [DNS重绑定攻击](https://zh.wikipedia.org/wiki/DNS%E9%87%8D%E6%96%B0%E7%BB%91%E5%AE%9A%E6%94%BB%E5%87%BB) 而开启的，大家有兴趣的可以自行搜索。会自动屏蔽解析出的内网 `ip` ，因此我们只要将其关掉，即可解析内网 `ip` 。

当然，更保险的方法是通过 `hosts` 或者其他方式指定。

这里附上 `DNS重绑定攻击` 的维基百科解释：

> **DNS重新绑定**是[计算机攻击](https://zh.wikipedia.org/wiki/计算机安全)的一种形式。 在这种攻击中，恶意[网页](https://zh.wikipedia.org/wiki/网页)会导致访问者运行[客户端脚本](https://zh.wikipedia.org/wiki/動態網頁)，攻击网络上其他地方的计算机。 从理论上讲，[同源策略](https://zh.wikipedia.org/wiki/同源策略)可防止发生这种情况：客户端脚本只能访问为脚本提供服务的同一主机上的内容。 比较[域名](https://zh.wikipedia.org/wiki/域名)是实施此策略的重要部分，因此DNS重新绑定通过滥用域名系统（DNS）来绕过这种保护。
>
> 这种攻击可以通过让受害者的网络浏览器访问专用IP地址的机器并将结果返回给攻击者来破坏专用网络。 它也可以用于使用受害者机器发送垃圾邮件，分布式拒绝服务攻击或其他恶意活动。



## 总结

本教程主要涉及了刷写原固件，breed，openwrt 的方案。

还介绍了一下关于 网络接口、科学上网、动态dns 等方面的设置方式。

容易注意到本教程未涉及 `IPv6` 的设置，可能是因为学校网络的原因，我设置 `IPv6` 久久没有成功，试了很多方法与固件，都没有成功。后来咨询了一下固件作者，猜测可能是因为学校网络的策略问题。分发 `IPv6-PD` 有问题，这个还没有查证，后续有时间可以再试一试。

教程说的比较罗嗦，可能也比较详细。大家如果还有什么地方不懂，欢迎留言询问！记得留下您的邮箱，这样可以及时的收到回复提示。

特别鸣谢各位制作固件与插件的 [hfy](https://www.right.com.cn/forum/space-uid-530002.html)，[small_5](https://www.right.com.cn/forum/space-uid-357532.html)，[hackpascal](https://www.right.com.cn/forum/space-uid-200302.html)，[hanwckf](https://github.com/hanwckf) 等大佬！

下面给出一些现阶段比较好用的固件，都是我搜索筛选过的，评价看起来都不错~



## 固件总览

### Openwrt

+ small_5发布的固件，功能基本是最完备的版本，比较稳定，插件都自己加工过，并且 `Overwall` 是独家的科学上网插件，不过基本没有什么教程，可以去群里问群主，人比较活跃（但是脾气有点暴躁）：

  + 恩山发布地址：https://www.right.com.cn/forum/thread-4751462-1-1.html

  + 固件下载链接：https://drive.google.com/drive/folders/1evev5nZet17WGozpvHg08SnIjuc-8Lq_

    一些插件链接：https://drive.google.com/drive/folders/1MPQrvubdGDwpIyyA4ZfO-aCXqIQLkFkb

  + 公告 tg 群：https://t.me/Firmware_Openwrt

+ 基于 Github Action 的自编译插件，不过近期不怎么维护了，用起来还不错，之前因为用不了 IPv6 换掉了，现在感觉是自己的问题，插件应该没啥问题

  + 恩山发布地址：https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=4048412&extra=page%3D2%26filter%3Dtypeid%26typeid%3D43
  + 固件下载连接：https://down.cloudorz.com/Router/LEDE/XiaoMi/XiaoMi-AC2100/
  + Github仓库地址：https://github.com/1orz/My-action

+ 功能很全的一个固件，但是其实我没用过，下面的评价看起来很不错：

  + 恩山发布地址：https://www.right.com.cn/FORUM/thread-4070200-1-1.html

+ 恩山上评价不错的一个插件，具有常用功能，但是没有什么教程：
  + 恩山发布地址：https://www.right.com.cn/forum/thread-4107734-1-1.html

+ 之前计划使用的插件，作者 hfy 做了很多的工作，好比制作网盘放各种固件、插件，写了很多教程，可是现在好像已经淡出了，固件无法使用，仅供参考
  + 恩山发布地址：https://www.right.com.cn/forum/thread-4027477-1-1.html
  + 发布地址：http://openwrt.ink:88/archives/openwrt-ac2100



### Padavan

+ 稳定而简洁的 Padavan，大家看起来评价都不错！
  + 恩山发布地址：https://www.right.com.cn/forum/thread-4532281-1-1.html
  + 下载地址：https://pan.baidu.com/s/1gyL54MDYtm7gOEUwY1fdrQ 提取码：pad2
  + 交流群：324762006
+ 用的最多的 Padavan 发布，更新的比较多，同步最新源码，功能齐全！
  + 发布地址：https://t.me/s/pdcn1



### Bootloader

+ [通用的breed](https://breed.hackpascal.net/breed-mt7621-xiaomi-r3g.bin)
+ [红米官方的 Bootloader](http://openwrt.ink:88/upload/2020/12/bootloader-redmi-ac2100-928443369e734b72b9611377b94598bb.bin)
+ [小米官方的 Bootloader](http://openwrt.ink:88/upload/2020/12/bootloader-xiaomi-ac2100-2ad34eb9357e4760948f797999b7e294.bin)
+ [刷机软件包等](http://openwrt.ink:8666/%E5%AE%98%E6%96%B9%E5%B7%A5%E5%85%B7%E5%8C%85/)

+ [breed源](https://www.right.com.cn/forum/thread-161906-1-1.html)

+ [官方2.0.7全固件](http://openwrt.ink:88/upload/2020/12/miwifi_rm2100_firmware_d6234_2.0.7-f693369307724965af27d3eda2458ad7.bin)
+ [官方2.0.376全固件](http://openwrt.ink:8666/%E5%AE%98%E6%96%B9%E5%B7%A5%E5%85%B7%E5%8C%85/%E5%B0%8F%E7%B1%B3AC2100/%E5%AE%98%E6%96%B9%E5%9B%BA%E4%BB%B6/miwifi_r2100_firmware_64288_2.0.376.bin)



### Source Code

+ [lede](https://github.com/coolsnowwolf/lede)
+ [openwrt](https://github.com/openwrt/openwrt)
+ [handwckf/rt-n56u](https://github.com/hanwckf/rt-n56u/)



## 参考链接

+ [恩山无线论坛](https://www.right.com.cn/forum/)

+ [【教程】红米&小米AC2100刷回官方固件方法及相关工具](http://openwrt.ink:88/archives/mirouter-xiufu)
+ [【教程】红米AC2100官方固件/OpenWrt/Padavan间互刷](http://openwrt.ink:88/archives/rm2100-shuaji)
+ [【Breed】红米AC2100&小米AC2100一键SSH刷Breed和任意固件教程](http://openwrt.ink:88/archives/s-breed)
+ [【0310更新】红米&小米AC2100多功能|精简|AP版下载](http://openwrt.ink:88/archives/openwrt-ac2100)
+ [红米/小米AC2100刷入r3g breed以及恢复官方boot详细教程（更新查坏块方法、pb-boot） - 小米无线路由器以及小米无线相关的设备 - 恩山无线论坛 - Powered by Discuz! (right.com.cn)](https://www.right.com.cn/forum/thread-4023907-1-1.html)
+ [红米AC2100刷机Padavan固件全图解，超超超详细_路由器_什么值得买 (smzdm.com)](https://post.smzdm.com/p/aoo85457/)
+ [小米AC2100和红米AC2100可以刷的breed。另附小米和红米官方的bootloader - 小米无线路由器以及小米无线相关的设备 - 恩山无线论坛 - Powered by Discuz! (right.com.cn)](https://www.right.com.cn/forum/thread-4023403-1-2.html)
+ [【Breed】红米AC2100&小米AC2100一键SSH刷Breed和任意固件教程 - 寒枫叶 (openwrt.ink)](http://openwrt.ink:88/archives/s-breed)
+ [HNU校园网IPv6免流折腾实录 - cyp0633's blog](https://cyp0633.icu/archives/53)
+ [小米 红米【AC2100】一键刷BREED【30秒刷完】小白脑残专用 无需工具TELNET + 检查坏块 - 小米无线路由器以及小米无线相关的设备 - 恩山无线论坛 - Powered by Discuz! (right.com.cn)](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=4066963&extra=page%3D1%26filter%3Dtypeid%26typeid%3D43)
+ [红米(小米)AC2100无需Telnet刷入Breed和Padavan固件教程 - 小米无线路由器以及小米无线相关的设备 - 恩山无线论坛 - Powered by Discuz! (right.com.cn)](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=4054150&extra=page%3D1%26filter%3Dtypeid%26typeid%3D43)
+ [2020-08-29更新\] 教程：小米和红米AC2100用breed刷openwrt - 小米无线路由器以及小米无线相关的设备 - 恩山无线论坛 - Powered by Discuz! (right.com.cn)](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=4025861&extra=page%3D7%26filter%3Dtypeid%26typeid%3D43)
+ [OpenWrt 配置 Cloudflare DDNS - P3TERX ZONE](https://p3terx.com/archives/openwrt-cloudflare-ddns.html)
+ [小米路由器青春版刷华硕固件](https://biji.io/2017/4963.html)


  [1]: https://blog.zzsqwq.cn/usr/uploads/2021/08/3364003385.png
  [2]: https://blog.zzsqwq.cn/usr/uploads/2021/08/402582447.png
  [3]: https://blog.zzsqwq.cn/usr/uploads/2021/08/402582447.png
  [4]: https://blog.zzsqwq.cn/usr/uploads/2021/08/3556294066.png
  [5]: https://blog.zzsqwq.cn/usr/uploads/2021/08/2555058433.png
  [6]: https://blog.zzsqwq.cn/usr/uploads/2021/08/3424062604.png
  [7]: https://blog.zzsqwq.cn/usr/uploads/2021/08/2622435560.png
  [8]: https://blog.zzsqwq.cn/usr/uploads/2021/08/996774304.png