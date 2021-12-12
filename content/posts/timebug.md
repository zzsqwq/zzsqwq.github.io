---
title: "Hugo 时区错误导致的文章无法发布"
categories: ["hugo"]
tags: ["hugo", "time"]
draft: true
slug: "222"
date: "2021-12-13 03:04:00"
---





## 起因

刚才发布博客的时候，使用 `hugo server` 在本地预览，死活刷不出来想要发布的博客。

## 解决方案

经过搜索后发现是由于我语言设置的是 en，这也导致我的时区出现了问题，与本地的时区不一致。

Hugo 的时间默认为格林威治时间，比中国的时间晚八个小时。

只需要在配置文件中加入 `buildfuture = true` 即可解决。

或者一次性的解决，使用 `hugo server --buildFuture` or `hugo --buildFuture`

