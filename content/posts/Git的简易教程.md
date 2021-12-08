---
title: "Git的简易教程"
categories: [ "Git" ]
tags: [ "Git" ]
draft: false
slug: "157"
date: "2020-12-05 20:26:00"
---



### 前言

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近在复习Git，因此顺手做个笔记分享出来，方便大家学习和查阅。相信无论是以后的课程作业还是工作，我们都会或多或少的接触/用到Git。



### 什么是Git？

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Git你可能没听说过，但是我相信你应该听说过Github，~~毕竟是全球最大的同性交友网站~~。他和Git有着密不可分的联系，我们后续再详细介绍。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Git的中文全称叫 **分布式版本控制系统** ，版本控制系统是什么意思呢？我们举一个简单的例子，你在做一个大作业，很可能要写上千行的代码，但是你可能写完一个功能以后，对他不够满意，但是又害怕改了以后后悔了，又找不回来了，所以你可能就有很多版本，版本1，版本2，版本3等等等。Git就是解决这个问题的，让你文件能够保持最新，但是又能恢复到之前的版本。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那么既然有分布式版本控制系统，就有 **集中式版本控制系统**，前者的代表是 **Git** ，后者的代表有 **SVN、CVS** 等。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;关于两者的区别，对于集中式版本控制系统，如果你想要对做一个项目的内容做修改，那么你要先从中央服务器把最近的版本拉取(Pull)下来，然后修改完以后，把修改后的版本推送(Push)上去，你本地只有最新的版本，而没有完整的版本库，分布式版本控制系统所作的工作与集中式的相差不大，只是它的本地会有一个完整的版本库，因此它十分的安全。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里贴一个他人总结的区别，供大家参考。

![集中式与分布式版本控制系统的区别][1]

### Git的安装

+ Linux系统

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为我只用过Ubuntu，所以我只会Ubuntu的QAQ..

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ubuntu中安装Git只需要在终端中输入  `sudo apt-get install git` 即可。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果是其他的linux系统，我猜你在终端中输入git即可获得安装提示，不然的话借助搜索引擎也可以。

+ Windows系统

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;直接去 [Git官网](https://git-scm.com/downloads)  下载安装程序然后安装即可。关于安装时候的选项，我都是用的默认的。

+ Mac OS

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;太穷了，没用过Mac，但是参考链接中给出了方法，大家有需求可以参考。



### Git的基本使用

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Git可以做许多事情，好比版本更新，版本修改，提交到远程仓库等等，这里我们只写一写大概以后用的会比较多的。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;需要注意的是，我们安装以后大概会有一个 **Git GUI** 还有一个 **Git Bash** ，开发中多用 **Git Bash**，下面的教程也是基于**Git Bash**的。

+ 表明身份

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Git所有仓库中我们都要有一个所有者的身份，用于标识是谁的仓库，用如下方式标识

  ```shell
  git config --global user.name "Your Name"
  git config --global user.email "Your email"
  ```

+ 创建版本库

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们想要把一个文件夹的内容用git来管理，只需要在文件夹目录运行

  ```shell
  git init
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;顾名思义这就是一个初始化的过程，运行以后在当前目录生成一个 `.git` 文件夹，里面是我们版本控制的数据，一般不要修改。

+ 把文件添加到Git的暂存区

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里出现了 **暂存区** 这个名词，Git内部的逻辑大概把我们工作的过程分为了三部分

  1. 一个是 **工作区** ，这个就是我们本地的文件夹。
  2. 一个是 **暂存区** ，这是我们把文件暂时放到暂存区里，没有决定更新我们的版本。
  3. 一个是 **最终分支**，这就是我们最终的版本存放的位置。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;贴一张廖雪峰老师教程中的一张图，我觉得还挺形象的。

  ![git-repo](https://static.liaoxuefeng.com/files/attachments/919020037470528/0)

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们清楚了上面的三个分区，那么考虑一下如何把文件夹中的文件从工作区推到暂存区，通过如下命令

  ```shell
  git add filepath
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们就可以把文件推到暂存区，最后的filepath就是我们需要推送的文件的路径，一般都用相对路径。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;上面是添加单个文件的方法，我们也可以把目录中所有做过更改的文件都加到暂存区中，就用

  ```shell
  git add .
  ```

+ 从暂存区推送到最后分支

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们可以把暂存区推送到最终分支，以完成我们的版本更新，通过

  ```shell
  git commit -m "description"
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;其中description的内容是我们对该次版本更新的一次描述，好比增加什么功能。

+ 版本回退

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那么既然我们每次有更新版本，我们如何从最新的版本回退到之前的某个版本呢？

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里我们需要了解到，我们有一个指针 `HEAD` 来指向我们当前的版本，因此我们只需要指定HEAD指针指向的版本，就可以做到更改版本。

  ```shell
  git reset --hard 版本号
  git reset --hard HEAD^
  git reset --hard HEAD~cnt
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里三个命令都可以回退版本，每一个版本有一个版本号，可以指定版本号直接回到对应的版本。此外，通过 `git reflog` 可以查看每个版本的版本号。 

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;第二个和第三个有点像，都是往前回退几个版本，有几个`^`就是回退几个，后面`cnt`是几就回退几个。

+ 分支结构

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Git支持分支结构，可以使我们的版本管理变得十分有序，以不至于逻辑混乱。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Git有一个初始主分支master，我们可以

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;创建分支

  ```shell
  git branch 分支名称
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;切换分支(两个命令都可以)

  ```shell
  git switch 分支名称
  git checkout 分支名称
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;创建并切换分支

  ```shell
  git checkout -b 分支名称
  ```

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;好了，学了这些，其实就已经够让我们在日常生活中管理文件了，下面说一些用于团队合作的。



### Github的作用

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;众所周知，一个大项目，想要一个人完成是很难的，大多数优秀的项目都是集思广益，大家一起出力建造出来的。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们上面提到的Github就是这样的一个平台，他是一个代码托管仓库，我们可以把新建一个仓库，然后把代码存储到上面，用的时候从上面Pull下来，而且只要经过你的同意，大家都可以为你的仓库贡献代码。由于这种开源的性质，Github有着许许多多优秀的项目，大家闲的没事可以去知乎搜搜Github上好玩的项目去玩一玩，好比 ~~狗屁不通文章生成器~~ 。



### Git与Github的协同

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Git和Github密不可分，我们可以使用Git往Github上推送代码，从上面拉取代码等。

+ 从Github克隆代码

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们如果想要从一个公有仓库中，把它的代码克隆到自己的本地，然后对他做一些应用。我们可以使用

  ```shell
  git clone 仓库地址
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这样就可以克隆仓库到本地，这里的仓库地址支持 `https` 或者 `ssh` 协议。

+ 推送本地仓库到Github

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先我们要在Github上有一个自己的仓库，才能将自己的代码推送上去，因此需要在Github上创建自己的公有/私有仓库。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;然后我们在本地仓库做完修改，`commit` 以后，输入如下命令

  ```shell
  git remote add origin 仓库地址
  git push -u origin master
  ```

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这两步一个是与远程仓库建立关联，一个是将代码push上去。

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通过这两步就可以把我们当地仓库推送到Github。



### Github的Pull Request

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;项目需要团队合作，**Pull Request**就是为了团队协作而生的。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你可以把他人的代码clone下来做完修改以后，向仓库所有者提交Pull Request，请求将自己的代码与他的代码合并，如果所有者同意，即可把你的代码合并到他的仓库中，以完成版本的更新，而你，就对这个项目做了一份贡献。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;那么如何提交Pull Request呢？

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于对仓库有所有权的人来说，只需要把代码clone下来，然后创建并切换到自己的分支，对当前分支进行修改后，push到仓库中，然后创建合并请求即可。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于非仓库所有者而言，你需要先把代码 `fork` 下来，然后clone你 `fork` 的仓库，关联对方的远程仓库，修改后推上去，然后创建合并请求。 

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;大家对开源项目的贡献多是用第二种，自己团队内的协作多是第一种。



### 一个总结

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这篇文章讲的内容不多，QAQ只讲了部分我们可能用的比较多的。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;关于Git的基本使用，还有很多内容，好比 `git merge` ，`git diff  ` 啥的，我们用的可能比较少，我就没提。大家如果有需求可以翻下面的参考链接进行学习。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;之后的内容多是团队协作会用到的，大家可以有需要的时候再看。学会使用Git可以有效地帮助我们管理文件，进行团队协作，提高工作效率。

​	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;关于后续的学习，大家可以选择 **廖雪峰老师的Git教程** ，实例丰富、简单易懂，此外还有 **Pro Git** ，内容丰富，涵盖了基本所有的命令，以及Github上的一个开源项目 **learnGitBranching** ，有着可视化的界面与闯关机制，趣味十足。



### 参考文档

+ [廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/896043488029600)
+ [Pro Git](https://git-scm.com/book/zh/v2)
+ [集中式(SVN)和分布式(Git)版本控制系统的简单比较](https://www.cnblogs.com/wx1993/p/7680877.html)
+ [git学习-Github上如何进行PR(Pull Request)](https://blog.csdn.net/qq_33429968/article/details/62219783)

+ [pcottle/learnGitBranching: An interactive git visualization to challenge and educate! (github.com)](https://github.com/pcottle/learnGitBranching)


  [1]: https://www.zzsqwq.cn/usr/uploads/2020/12/2911747011.png