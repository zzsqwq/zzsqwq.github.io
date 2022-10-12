---
title: "Ubuntu18.04优化教程"
categories: [ "Ubuntu" ]
tags: [ "Linux","主题配置","ubuntu" ]
draft: false
slug: "141"
date: "2020-12-04 01:39:00"
---




### 前言

​	因为最近Ubuntu用的比较频繁，所以前一阵把Ubuntu16.04换成Ubuntu18.04了，并且囿于机械硬盘那启动速度，我忍痛割爱把我80G的固态硬盘分给了Ubuntu。

​	后来，用着用着就觉得这个Ubuntu的原始界面确实不是特别的好看，~~配色偏基佬紫的感觉~~。“工欲善其事，必先利其器“，我们只有将自己的工作环境布置的舒心一些才能有做下去的动力！所以我想给Ubuntu换一个看起来舒服点的界面，然后上网找教程乱七八糟的倒腾了一会，感觉换完以后完全不一样了，这个界面真的好看！！用起来也特别的舒心，感觉自己马上就要告别Windows投奔Linux的怀抱了。后续还有一些其他的优化，例如装QQ、微信、配置终端等，一并写在这里吧。

### Ubuntu界面的优化

#### 具体效果

​	先放几张效果图上来，是我改后的界面。大概就是这样(自我感觉挺好看的)，当然也有其他的主题可供选择。

![桌面风格][1]

![文件夹风格][2]

![效果图][3]

#### 1.安装GNOME桌面环境主题配置工具	

​	如果要改主题，那么首先要有一个利器，这里我用的`Ubuntu18.04`，桌面环境为 `GNOME 3.28.2` ，因为我目前接触的只有GNOME桌面环境的，Ubuntu18.04本来的桌面环境就是GNOME，但是Ubuntu16.04好像没有自带，但是可以安装，这里大家可以自行百度了解。

​	然后我们已经有了GNOME桌面环境后，安装主题配置工具 `GNOME Tweaks ` ，在终端中输入如下内容：

```shell
sudo apt-get update
sudo apt-get install gnome-tweak-tool 
```

​	我们先更新软件源，然后安装后直接打开他就行，在系统软件中中文大概叫 **优化** 。

#### 2.拓展上述工具

​	安装完上述工具后，我们可能发现了一个问题，就是外观那一栏目的Shell有一个感叹号，无法更改，这是因为我们没有安装拓展导致的。我们在终端中运行

```shell
sudo apt-get install gnome-shell-extensions
```

​	然后重启一下电脑。再打开软件找到左侧的拓展，把 `User themes` 那一栏目打开。切换回去就可以发现Shell那边的感叹号无了。

#### 3.寻找自己喜欢的主题

​	这里我大家可以去这个网址去找自己喜欢的 [GNOME-LOOK.ORG](https://www.gnome-look.org) 

​	这里面包含了图标，主题这些，下面介绍一下如何安装。

​	好比我们找到一个自己喜欢的主题，然后我们点击下面的 **Files** ，可能会有很多文件，但是多是同一个主题的不同风格，好比暗风格和亮风格这样的，还有不同的版本的，我多是安装那个下载量最多的，我们下载那个对应的文件(多是tar.xz安装包)。

​	对于主题的安装，我们只需要把解压出来的文件，移动或复制到 `/usr/share/themes/` 目录下，如果是光标\图标的安装，那么就把文件夹移动到 `/usr/share/icons` 目录下。

​	然后我们回到 `GNOME Tweaks` 软件中就可以发现，我们已经可以在主题\图标\光标\Shell清单中找到我们移动到文件夹中的文件了，然后选择就可以切换了。这里需要注意的，很多主题都是自带Shell的，你下了一个主题，那么你可以在Shell和主题这两个栏目中都找到他们，是一个配套的。

#### 4.一些后续的调整

​	我们后续可以改变左边收藏夹的位置，我觉得放在左边有一丢丢的丑，所以我选择把它放在的下面。

​	我们去Ubuntu软件中搜索 Dash to dock，然后安装这个拓展，然后打开 `GNOME Tweaks` 软件在拓展中找到他就可以随心所欲的调我们的收藏夹的位置了。

#### 5.我自己的配置

```
theme&shell   Canta-light-compact
icons 01-McMojave-circle
```

#### 6.界面修改的参考链接

+ [Ubuntu18.04美化主题(mac主题)](https://blog.csdn.net/lishanleilixin/article/details/80453565)
+ [Ubuntu18.04美化主题(完整版)](https://blog.csdn.net/qq_42527676/article/details/91356154)
+ [GNOME-LOOK.ORG](https://www.gnome-look.org)
+ [30个非常不错的Ubuntu主题供你选择](https://www.jianshu.com/p/4fb5e4657695)
+ [ubuntu18.04更换鼠标游标主题](https://blog.csdn.net/maodexuedinge_/article/details/106652323)



### 配置终端

#### 前言

​	唔，终端本来用起来感觉也还行，感觉终端就是linux的灵魂，啥都能干。

​	但是听说有更好用的终端，现在ubuntu普遍用的好像都是bash，但是好像还有zsh，fish这样的，他们可以有一些代码补全，功能更强，可拓展性也高，大家都zsh和fish哪个好用一直争执不停，但是我发现ROS对 `zsh` 支持的很好，但是对 `fish` 的支持有点拉胯，考虑到以后可能ros会用的比较多，因此我选择使用zsh。效果图如下

​	![终端效果图][4]

#### 1.下载zsh

​	直接在终端执行下列语句

```shell
sudo apt-get update
sudo apt-get install zsh
```

#### 2.安装oh my zsh

​	zsh其实配置起来很麻烦，这一点相比与fish不太行，fish下载下来就已经具备了常用的功能，例如代码补全等。

​	但是我们有先人铺路，在Github上有一个开源的项目 [oh my zsh](https://github.com/ohmyzsh/ohmyzsh)，就是专门用来导入这个zsh的配置的，

​	我们直接运行如下代码，就可以直接安装了

```shell
wget sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

​	然后我们可以通过在终端中输入zsh来切换到zsh，也可以通过输入bash切换回去

​	我们也可以通过如下命令来切换默认的终端系统，首先查找一下zsh的目录，然后切换过去。

```shell
whereis zsh
chsh -s zsh路径
```

#### 3.配置插件

​	在zsh里面有许多好用的插件，在这里推荐两个

+ 自动补全 [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)
+ 语法高亮 [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

​	关于下载的方法，在这两个项目里面都说了，我们只需要把下载的文件放到  `~/.oh-my-zsh/plugins` 目录中，然后编辑 `~/.zshrc` 在 `plugin = (插件1 插件2)` 写上你所要启用的插件名称即可。

​	然后在终端中 `source ~/.zshrc` 更新一下配置即可。

#### 4.配置主题

​	zsh有许多可用的主题选择，我自己最喜欢的一个叫 [pure](https://github.com/sindresorhus/pure)

​	我觉得害挺好看的，效果就跟上图一样吧。

​	安装方法就是先把pure下载到一个路径，然后编辑 `~/.zshrc`

​	在下面加入

```
fpath+=安装路径
autoload -U promptinit; promptinit
prompt pure
```

​	然后我们`source`一下`.zshrc` 就可以使用了。

​	但是如果是使用zsh本来自带的一些主题，和这个有些差别，只需要下载好主题然后放到 `~/.oh-my-zsh/themes` ，然后在 `.zshrc `中启用即可。

#### 5.参考链接

+ [linux终端shell:zsh配置和使用](https://www.cnblogs.com/rainy0426/articles/13163167.html)
+ [zsh和oh-my-zsh的一些好用的主题和插件](https://blog.csdn.net/shengzhu1/article/details/55272234/)
+ [linux安装oh my zsh终端及简单使用方法](https://blog.csdn.net/yuqiongran/article/details/52280522)
+ [配置oh-my-zsh主题](https://www.jianshu.com/p/497b4af1334d)
+ [Zsh 常用插件](https://www.jianshu.com/p/a94e2c59f244)

+ [给Zsh添加主题和插件](https://linux.cn/article-11426-1.html)
+ [常用的oh-my-zsh插件](https://zhuanlan.zhihu.com/p/61447507)


还有一些其他的配置有时间再写吧……咕咕咕

  [1]: https://blog.zzsqwq.cn/usr/uploads/2020/12/171170690.png
  [2]: https://blog.zzsqwq.cn/usr/uploads/2020/12/3724154221.png
  [3]: https://blog.zzsqwq.cn/usr/uploads/2020/12/3559388649.png
  [4]: https://blog.zzsqwq.cn/usr/uploads/2020/12/3375893306.png