---
title: "一个基于 Hugo 的个人主页主题"
categories: ["博客主题"]
tags: ["hugo","academia","theme"]
draft: false
slug: "my-hugo-academia-theme"
date: "2022-05-03 21:40:00"
typora-root-url: ../../static
---

## 背景

之前看到过学长学姐做过个人主页，多是用来申请一些学校的夏令营使用的，觉得非常的 Nice，自己也想搞一个。

大家之前好像大多用的是基于 Hexo 的一个主题 — [hexo-theme-academia](https://github.com/PhosphorW/hexo-theme-academia)，但是由于之前我用过 Hexo，觉得他有一些比较明显的弊端，例如环境配置比较麻烦、需要安装 Nodejs、npm 等环境，然后再安装 Hexo。其次它的构建速度比较慢，用起来感觉比较僵硬。

后来随着了解增多，尝试了 Typecho、WordPress，Hugo 等主题后，目前还是决定使用 Hugo。它构建速度快，而且安装简单，在 Ubuntu 上只需要一行 `sudo apt install hugo` 即可，不可谓不简单。因此萌生了移植一个 Hugo 版本主题的想法，刚好可以锻炼一下自己。

欢迎点击 [这里](https://zzsqwq.cn) 查看我的个人主页。


## 一些难点

之前从来没有了解过 Hugo 主题的写法以及 Hexo 主题的写法，不过看了一下仓库的组织形式还算好理解。

这类静态博客生成器都是需要写一些模板文件，然后根据配置文件进行个性化构建。

[Hugo 的文档](https://gohugo.io/documentation/) 十分的完善，学习就像是学习一门编程语言，里面有很多函数和变量，还有各种条件结构、循环结构等。原主题是采用的 pug + stylus 的方式，而不是传统的 html + css 。不过这两者之间的转换并不麻烦，而且有一些工具可以参考着转换，例如 [pug2html](https://pughtml.com/) 以及 [stylus2css](https://verytoolz.com/stylus-css.html) 。

后续就参考着一点点的移植就可以，同时我也改写了一下配置文件（使用的 yaml 格式），大概是更易于配置了。

同时，得益于 Hugo 的强大，我很方便的完成了对多语言的支持。



## 最终效果

目前已经更新到了 v1.1.0 版本，欢迎大家体验，有问题可以及时反馈！

主题链接：https://github.com/zzsqwq/hugo-academia-theme

演示站：https://zzsqwq.github.io/academic-pages-demo/

英文文档：https://github.com/zzsqwq/hugo-academia-theme/blob/master/README.md

中文文档：https://github.com/zzsqwq/hugo-academia-theme/blob/master/README.zh_cn.md

英文效果图：

![demo-en](/images/hugo-academia-theme/demo-en.png)

中文效果图：

![demo-zh_cn](/images/hugo-academia-theme/demo-zh_cn.png)
