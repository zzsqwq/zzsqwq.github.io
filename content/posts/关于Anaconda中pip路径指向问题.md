---
title: "关于Anaconda中pip路径指向问题"
categories: [ "Ubuntu" ]
tags: [ "Python","conda","pip" ]
draft: false
slug: "169"
date: "2021-03-06 20:51:00"
---

### 前言

最近使用Anaconda的时候遇到了一个很奇怪的问题，如当我新建环境 **condatest** 后，使用 `pip -V` 查看pip的路径指向，会发现pip指向的是另一个环境 **CenterNet** 的路径。搜索了很久得到一个有一些用的解决方法

### 解决方案

设有问题的环境为 **condatest** ，python版本为 **3.6**

然后进入 `~/anaconda3/envs/condatest/lib/python3.6`

编辑目录下的 `site.py` 文件，将其中的 `USER_SITE` 的值修改为 `/home/zs/anaconda3/envs/condatest` ，注意这里路径里面的 `zs` 是你的当前用户名， `USER_BASE` 的值修改为 `/home/zs/anaconda3/envs/condatest/lib/python3.6/site.py`，然后问题应该就解决了。

因为如果环境问题的话，上面两个字符串都为空，猜测的原因是因为有同python版本的环境导致默认指向错误，此方式为修改conda中pip的指向。

### 参考链接

+ [更改conda环境中的pip包安装的默认路径](https://www.pythonf.cn/read/51713)

