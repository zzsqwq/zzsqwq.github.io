---
title: "如何善用搜索引擎？"
categories: ["study"]
tags: ["search engine", "skills"]
draft: true
slug: "225"
date: "2022-4-9 02:31:00"
---

## 前言

**如何善用搜索引擎？**

我们使用搜索引擎可能是为了搜集信息、或者是解决一些问题。但如果不能正确的使用搜索引擎，使用关键字等，可能搜到的就可能相关不大，或者与我们的问题相差甚远。

这篇文章意在分享我个人在学习、工作中使用搜索引擎的一些技巧与思考，如有纰漏，还请大家指正。

## 如何正确的知道问题所在？

我们遇到一个问题，要首先能够定位这个问题的关键。

例如编译中出错了，可能有很多 log，你必须能够精确定位到到底哪一条 log 才是这次编译出错的关键，进而才能解决这个问题。

以 CMake 为例，编译时报错 log 一般为<font color="red">红色</font>，警告 log 一般为<font color="yellow">黄色</font>，普通 log 一般为白色。得益于这种等级/颜色分明的 log ，我们可以快速的定位报错。我们日常编程中，为了便于 Debug，最好也遵循这个规范，例如在 `C++` 中打印错误 log 时推荐使用 `std::stderr` 而不是 `std::stdout` 。

 