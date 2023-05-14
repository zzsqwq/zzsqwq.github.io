---

title: "Linux 上 Clash For Windows 如何设置开机自启与桌面图标"
categories: ["Linux","Clash"]
tags: ["Clash","开启自启动","桌面图标"]
draft: true
slug: "clash-for-windows-on-linux"
date: "2022-05-12 22:37:00"
typora-root-url: ../../static
---

## 背景

之前写过一篇 [如何在 Linux 上优雅的使用 Clash？ ](https://blog.zzsqwq.cn/posts/how-to-use-clash-on-linux/)的博客，不过用了一段时间后这么倒腾还是不如直接用 Clash For Windows 这种方便的图形化界面，主要是改配置文件啥的方便，不需要重启啥的。

配了一下发现 Linux 版本的 Clash For Windows 貌似打开 Start with Linux 没什么用处，因此顺便写个配置教程。

Clash For Windows 的具体使用可以查看官方教程：https://docs.cfw.lbyczf.com/

顺便说一句，Linux 版本的无法自动设置系统代理，需要自己手动设置，不过我推荐你使用 `proxychains` 等工具在终端进行代理，使用 `SwitchyOmega`  等工具在浏览器端代理。

## 安装 Clash For Windows

首先在 Linux 上下载 Clash For Windows，

Github release 链接：https://github.com/Fndroid/clash_for_windows_pkg/releases

在其中找到需要的版本，然后下载即可，如果国内下载较慢的话，可以使用大佬提供的代理[^1]，使用方式是在下载链接的前面加上 `https://ghproxy.com/` 即可，例如一个下载链接为 `https://github.com/aaa` ，则代理后的链接为 `https://ghproxy.com/https://github.com/aaa`

下载后应该是名为 `Clash.for.Windows-version-x64-linux.tar.gz` 的文件，然后解压：

```bash
❯ tar -zxvf Clash.for.Windows.xxx # 这里是对应压缩包的名字
```

解压后会出现对应的文件夹，文件夹中会有一个名为 `cfw` 的文件，按理说现在在文件中直接打开终端，然后运行

```bash
❯ ./cfw 
```

就会直接打开 Clash For Windows 了，与 Windows 版本基本平常无差。

接下来最好可以链接一个启动项来方便配置，不过，是可选的：

```bash
❯ mkdir -p ~/.local/bin
❯ ln -s /home/user/Downloads/xxxx/cfw /home/user/.local/bin/cfw 
```

注意，这里一定要用绝对路径！同时，这里的 user 是指你的用户名，下同。

简单解释一下，这里首先创建了本地的一个 bin 目录，然后软链接了我们解压出的 `cfw` 到对应路径下，注意不要直接 `cp` 过去，会找不到动态库。

Ps：Ubuntu 的策略貌似是如果不存在 `~/.local/bin` 目录那么是不会添加到环境变量的，但是如果有了，重启就会自动添加。因此现在可能没法在终端直接输入 `cfw` 来启动 Clash，重启才行，不过没事，对下述过程没影响。

## 配置快速启动图标

每次在终端打开当然是有点烦的，因此最好可以直接在快速启动栏找到，像下面这样：

![](/images/clash-for-windows-on-linux/desktop.png)

和我之前写过的 [Ubuntu如何利用AppImage创建Qv2ray快捷方式](https://blog.zzsqwq.cn/posts/193/) 差不多，

```bash
❯ sudo vim ~/.local/share/applications/clash.desktop # 编辑一个文件
# 写入如下内容
[Desktop Entry]
Name=Clash Fow Windows
Exec=/home/user/.local/bin/cfw
Icon=/home/user/.local/bin/cfw
Type=Application
StartupNotify=true
```

然后添加可执行权限：

```bash
❯ sudo chmod +x ~/.local/share/applications/clash.desktop
```

这里也是在 `.local` 文件夹下操作的，这样只会影响到当前环境，当然你也可以在 `/usr/share/applications` 路径下创建。可以在这里[^2] 找到对应的 Clash Logo，在 Icon 那里设置图标路径即可，如果还是不行，可以找到这个文件然后右键->属性，点击图标更改。

![](/images/clash-for-windows-on-linux/set_icon.png)

## 配置开机自启动

{{< notice info >}}  在 2022/6/4 的更新中，已经修复了 Start with Linux 失效的问题。 {{< /notice >}}

因为 Electron 的一些限制，点击 `Start with Linux` 后貌似仍没法开机自启动，去官方仓库看了一下 Issue，找到了两个：

1. [ubuntu下开启start with linux没有作用](https://github.com/Fndroid/clash_for_windows_pkg/issues/2856)
2. [在 Linux 上安装 | Install on Linux](https://github.com/Fndroid/clash_for_windows_pkg/issues/2230#issuecomment-922433716)

采取 [horizon86 ](https://github.com/horizon86)大佬的方式

> 我的临时解决方案是添加`/etc/xdg/autostart/cfw.desktop`，内容如下：
>
> ```bash
> [Desktop Entry]
> Name=CFW
> Name[zh_CN]=Clash for windwos
> GenericName=Clash for windows
> GenericName[zh_CN]=Clash for windows
> Comment=Start Clash for windows
> Comment[zh_CN]=启动Clash for windows
> Exec=/home/user/.local/bin/cfw
> Icon=/home/user/.local/bin/cfw
> Terminal=false
> Type=Application
> Categories=System;Utility;
> StartupNotify=false
> X-GNOME-AutoRestart=false
> X-GNOME-Autostart-Notify=false
> X-KDE-autostart-after=panel
> X-KDE-StartupNotify=false
> ```

注意这里的 `Exec`、`Icon` 字段保持与之前桌面启动图标中的一致即可，同样添加可执行权限即可

```bash
sudo chmod +x /etc/xdg/autostart/cfw.desktop
```

[^1]: https://ghproxy.com/
[^2]: https://zh.wikipedia.org/zh-sg/File:Clash_Logo.png
