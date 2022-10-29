---
title: "如何善用搜索引擎？"
categories: ["Study"]
tags: ["Search engine", "Skills"]
draft: false
slug: "how-to-search"
date: "2022-10-30 00:31:00"
---

## 前言

我们使用搜索引擎可能是为了搜集信息、或者是解决一些问题。但如果不能正确的使用搜索引擎，使用关键字等，可能搜到的答案就与问题相关不大，或者与我们的问题相差甚远。

这篇文章意在分享我个人在学习、工作中使用搜索引擎的一些技巧与思考，可能后续会不断的更新，如有纰漏，还请大家指正。

## 应该使用什么搜索平台？

想要提高搜索的效率，选择一个好的搜索引擎或者内容平台颇为重要。

下面列举的是我平时会使用的一些平台，列出的顺序代表了我个人对于他们的推荐程度：

+ 通用搜索引擎：[Google](https://google.com)、[Bing](https://bing.com)、[DuckDuckGo](https://duckduckgo.com)、[Baidu](https://baidu.com)
+ 编程相关问题：[Stack Overflow](https://stackoverflow.com)、[Github Issue](https://github.com)、[segmentfault](https://segmentfault.com)、[稀土掘金](https://juejin.cn)、[腾讯云开发者社区](https://cloud.tencent.com/developer)、[CSDN](https://www.csdn.net)、[华为云开发者社区](https://developer.huaweicloud.com)
+ 日常问题：[V2EX](https://www.v2ex.com)

第一类为通用搜索引擎，例如 *为什么最近头发掉的更多了？* 在其上可以搜索任何问题，其中 Google 的使用门槛相对较高，而 Bing 针对国内用户来说是一个相对不错的选择，不建议常用 Baidu，因为其上广告非常多，有用信息密度相对较小。

第二类为编程相关问题，例如 *CMake 为什么报错了？* 其中 Stack Overflow、Github 虽均为国外的平台，但是国内是可以访问的，不过在 Baidu 搜索引擎中词条相对靠后，**同时也需要使用英文来进行问题检索**。（Btw，Stack 系列还有例如 [Stack Exchange](https://stackexchange.com) 平台，它的模式与前者相同，但是更偏日常一些。）除此以外，后面的平台均为国内平台，所列是我平时常见的一些，整体而言，前面的文章质量普遍会比后面的高一些，其中腾讯云开发者社区、CSDN、华为云开发者社区三者内容重叠度较高。

第三类为日常问题，例如 *Mac 上有什么好用的 App* ？可以在 V2EX 上寻找答案，当然，它也是包罗万象的，只不过我常用它来解决日常各类小问题。

## 如何正确的知道问题所在？

我们遇到一个问题，要首先能够定位这个问题的关键。

例如编译中出错了，可能有很多 log，你必须能够精确定位到到底哪一条 log 才是这次编译出错的关键，进而才能解决这个问题。

以 CMake 为例，编译时报错 log 一般为<font color="red">红色</font>，警告 log 一般为<font color="yellow">黄色</font>，普通 log 一般为白色。得益于这种等级/颜色分明的 log ，我们可以快速的定位报错。我们日常编程中，为了便于 Debug，最好也遵循这个规范，例如在 `C++` 中打印错误 log 时推荐使用 `std::stderr` 而不是 `std::stdout` 。

见过很多身边的同学遇到了报错，就一股脑粘贴所有的报错 log，然后贴到搜索引擎中，发现相关的错误基本没有。这就是没有其中错误的关键，报错时的 log 可能 100 行只有 10 行是关键的，甚至只有一行是关键的，例如 100 行 log 中只有 **未定义的引用(undefined reference to 'xxx')** 这一句是最关键的。

## 搜索不到怎么办？

即使你选择了正确的平台，筛选出了关键的报错信息，你可能仍然无法搜索到对应的问题，这怎么办呢？

首先，你可能需要转换一下语言，例如之前问题采用中文，你应该将其翻译成英文再重新搜索，**记得翻译时要保证专有名词的正确性**。

如果还是不行，这个时候我认为应该首先静下心来思考一下这是属于什么问题，是这个程序特有的问题，还是这个程序使用的某个库或部件导致的问题？你可能需要仔细思考来进一步提取错误（问题）中的关键所在，使用提取的关键字继续搜索或者去查对应的程序或者库的 Q&A ，如果还是无法找到，就应该去看相应部分的文档，去 RTFM（Read The Fantastic Manual）。

最后的最后，还是无法找到的话，那你就应该考虑去对应程序、库或部件的 Github Issue 或者论坛、或非定向的 Stack Overflow 等平台提交问题，在提问之前，你最好仔细阅读过 [提问的智慧](https://github.com/ryanhanwu/How-To-Ask-Questions-The-Smart-Way/blob/main/README-zh_CN.md)[^1]，**不然可能无法获得到热情、友善的回复**。

![Microsoft Edge Question](/images/how-to-search/microsoft_edge_question.png) 

上图所示是我之前 [有关 Microsoft Edge 的一个提问](https://techcommunity.microsoft.com/t5/discussions/microsoft-edge-crash-when-i-login-in-my-microsoft-account-linux/m-p/3284922) ，应该可以算是一个相对正确的示范。

## 最好积累一个错误文档

感觉这个作用有点类似于高中时候老师们推崇的错题本了，虽然我一直觉得没啥用，也几乎没有照做过。

但是在平常解决问题时我确实是会遇到过，上次明明遇到过类似的问题，我也解决了，但是再遇到又忘了如何解决了。可能是因为随着年纪的增大记性越来越差了🤣，不过如果大家不嫌麻烦可以养成每次遇错都能记录一下，估计坚持下来会非常的有用（虽然我自己都没做到）。

[^1]: English version: http://www.catb.org/~esr/faqs/smart-questions.html
