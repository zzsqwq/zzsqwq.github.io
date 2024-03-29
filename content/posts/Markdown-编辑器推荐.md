---
title: "Markdown 编辑器推荐"
categories: [ "随笔" ]
tags: [ "markdown","marktext","typora" ]
draft: false
disableComments: false
slug: "220"
date: "2021-12-01 20:12:00"
---

# 前言

近期著名 Markdown 编辑器 [Typora](https://typora.io/) 宣布收费了，起初感觉很难受，后来感慨之余也觉得算是合理，毕竟 Typora 用起来感觉是真的很良心，也在考虑是否买一份支持一下。

> 已于2021/12/10购入，还是选择回归了 Typora 了哈哈哈

虽说左右分屏的设计可能更符合 Markdown 的初衷，但是像 Typora 这种所见即所得（WYSIWYG） 的书写体验确实是感觉习惯了就回不去了。

因此近期也搜集了 Markdown 编辑器作为 Typora 的替代品，在这里给大家分享一下。

**特别声明，下面的分享多是我日常体验中的一些感受，可能不够客观，比较片面，大家可以自己使用体验一下！**

# 一、Obsidian

## 界面预览

![Obsidian][1]

## 特点

- 具有文档的双向链接

- 支持行级和块级公式

- 官网可以购买 [sync](https://obsidian.md/sync) 套餐保持各个客户端同步

- 可以购买 [publish](https://obsidian.md/publish) 服务将 markdown 发布为排版美观的界面

- **有丰富的插件**，例如日历、待办清单、Git同步等等。

- 有Linux，Windows，**iPad**等多平台支持。

## 使用体验

[Obsidian](https://obsidian.md/) 中文为黑曜石。我觉得它的图标很好看。

在我使用的一段时间内，他给我的感觉是，功能十分丰富的一个 Markdown 文件管理工具。如果你购买了他的同步服务，那你可以很轻松的在各平台同步你的 Markdown 文件夹，并且基于他强大的插件，可以很完成很多 Markdown 文件份外的事，例如待办清单、日历等等。

他虽没有所见即所得的模式，但是依靠其一款第三方插件，可以达成类似的效果，不过还是用起来不如 Typora 这种顺手。同时，**它的各端同步如果不开启官方的服务，用起来还是挺麻烦的**，经过我的一阵倒腾，我总结了如下几个方案：

1. 使用第三方Git管理插件，可以定时推送文件夹中的内容到仓库，这样可以完成 linux 与 Windwos 平台的同步，只需要在某一方推送某一方拉取即可。而 Windows 平台与 iPad 平台的同步，可以借助 Apple 的 iCloud，Windows上有对应的客户端，这也是 Obsidian 官方支持的。不过在我使用的过程中我发现，这样异常的麻烦，使用 Git 来管理很可能会产生冲突，导致需要手动处理冲突，久而久之就会觉得很烦。

2. 使用自建云盘如 **NextCloud + Obsidian**，或者 Seafile + Obsidian。 这个是我觉得目前非常可行的一个方案，最近我也有在尝试 NextCloud，它的多端同步做的非常不错，依靠此可以在各个平台同步文件夹，加上 Obsidian 强悍的能力，是不错的组合！

# 二、Mark Text

## 界面预览

![Mark Text][2]

## 特点

- **开源！！**

- 所见即所得（WYSIWYG）

- 支持行级和块级公式

- 界面简洁且美观

- Windows，Linux 等多平台支持，不支持 iPad

- 缺点：**目前仍不支持中文**

## 使用体验

[Mark Text](https://marktext.app/) 是 Github 上一个开源的[项目](https://github.com/marktext/marktext)

个人认为他是在对标 Typora 的一个软件，有着和 Typora 非常相近的写作手感，并且界面简洁美观，我个人真的是非常喜欢，也是我目前在用的一款编辑器，本篇文章就是使用此编辑器书写。

它的可配置程度虽然没有 Typora 那么高，但是平常的使用已经足够。不过值得说道的是，他不支持导出 Word 文档，而且对用公式的补全做的不够完美。

可能还是有一些 bug 的，软件的最后一次 release 还是在 2020 年了（~~说明非常的稳定啊哈哈~~）。不过贵在他是个开源的软件，有很多大佬愿意为之奉献，期待后续的更新！

# 三、Zettlr

## 界面预览

![Zettlr][3]

## 特点

- **开源！！**

- 可导出的格式非常的多，如 Latex、Word 等都可以。

- 可以说是所见即所得，不过和 Typora 的理念略有不同。

- 支持 Windows、Linux 等平台。

- 支持语言的种类要比 Mark Text 多很多。

- **缺点**：自认为界面没有 Mark Text 和 Typora 这种好看。

## 使用体验

上面的有个特点我描述的是 **“可以说是所见即所得，不过和 Typora 的理念略有不同”**

我感觉**它的所见即所得不是纯正的所见即所得**，还是会保留部分源代码的元素在上面。不过他支持导出的格式要比 Mark Text 多不少。

同时他也支持任务清单这种小功能，但是我认为它没有 Mark Text 美观和好用。使用的也不算太多，就不过多的评价了~

# 四、VSCode + 插件

## 界面预览

![其中一个插件（vscode all markdown）][4]

## 特点

- 基于 VSCode 这个~~宇宙第一编辑器~~，不需要装别的软件

- 可选的插件很多，不差这个一个

- 多是基于 [vditor](https://github.com/Vanessa219/vditor)，对 vditor 有钟爱的同学不要错过！

- 所见即所得

## 使用体验

因为我自己没有深度体验这个东西，但是我认为功能还是很强大的~

虽然说，他可能没有一个单独的软件配置项那么多，但是贵在他只是一个集成于 VSCode 的插件，不要安装一个那么大体量的软件。

同时可以很快的在代码与文档之间切换，这应该也算是一个优势了。

# 后记

本来想把这个博文做成一个各类软件推荐文的，但是写着写着发现光是 Markdown 类的已经可以写很多了，为了防止篇幅过长，就单做一个 Markdown 编辑器的推荐文吧~

如果大家有更好的 Markdown 编辑器推荐，欢迎在下面留言！

[1]: https://blog.zzsqwq.cn/usr/uploads/2021/12/1150718291.png
[2]: https://blog.zzsqwq.cn/usr/uploads/2021/12/3145024348.png
[3]: https://blog.zzsqwq.cn/usr/uploads/2021/12/3408881163.png
[4]: https://blog.zzsqwq.cn/usr/uploads/2021/12/1511072141.png