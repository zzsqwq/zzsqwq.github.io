---
title: "使用 OrangePi 4 LTS 做旁路由"
categories: ['OpenWrt']
tags: ['RK3399', 'OrangePi4-lts', 'OpenWrt']
draft: false
slug: "orangepi4-lts-with-openwrt"
date: "2022-12-31T16:34:42+08:00"
typora-root-url: ../../static
---

## 前言

最新同居的学长从香港带了个最新版的 Apple TV 回来，但是 Apple TV 在国内用不了，必须要代理才可以，所以考虑到了路由器代理的方案。但是现在家里在用的路由器是 Redmi AX3000，没法刷固件，也不想再买个 R2S 这种软路由了，因此就计划将公司之前用完的 [香橙派(OrangePi) 4 LTS](http://www.orangepi.cn/html/hardWare/computerAndMicrocontrollers/details/Orange-Pi-4-LTS.html) 利用一下。它的芯片是 RK3399，用来做路由器绰绰有余，不用可惜了。

![旁路由成品图](/images/orangepi4-lts-with-openwrt/orangepi4-lts.jpeg)

## 为什么是旁路由？

不熟悉旁路由的同学可以看篇指南：[从听说到上手，人人都能看懂的旁路由入门指南](https://sspai.com/post/59708)

---

找了一些常见的固件，貌似只有针对于 OrangePi R1 和 OrangePi R1 Plus 编译的软路由固件，并且讯龙官方也有放出针对这两个板子的 Openwrt 固件[^1]，可以说是就是为软路由而生，价格也比较便宜，200 左右，性能和 R2S 差别不大。

试了一下在 4 LTS 上跑 R1 和 R1 Plus 的固件[^2]，都无法正常点亮，可能可以自己编译一个对应架构的 OpenWrt，但是有点麻烦，还是没尝试。

考虑到 4 LTS 上可以使用 Docker，因此可以在上面用 Docker 跑一个 OpenWrt 然后做旁路由，到时候只需要把 Apple TV 或者路由器的网关和 DNS 改一下就可以，因此最终选择使用旁路由方案。

## 具体方案

这里直接参考了小苏的教程：https://mlapp.cn/376.html，写的很清晰，而且很好的考虑了没有基础的同学，赞一个！

里面写的很详细，但是需要注意的是，因为香橙派4 LTS是 ARMv8 架构的，但是直接拉教程里面的默认 latest 镜像是 ARMv7 的，因此需要指定一下镜像的版本，具体的镜像版本可以看 Docker Hub 中的[介绍](https://hub.docker.com/r/sulinggg/openwrt)。

```bash
docker pull registry.cn-shanghai.aliyuncs.com/suling/openwrt:armv8
---
docker run --restart always --name openwrt -d --network macnet --privileged registry.cn-shanghai.aliyuncs.com/suling/openwrt:armv8 /sbin/init
```

除此以外，根据它的指导来基本就不会有问题，其中如果需要给路由器下所有设备代理就把路由器的网关和 DNS 改为旁路由的 IP，如果是单独给某个设备（例如只给 Apple TV 终端），就可以只给单个设备设置 DNS 和 网关。

## UDP 代理

我们想在 Apple TV 上联机玩狂野飙车8，启动多人对战一直报加入服务器错误，观察了一下发现应该是 UDP 流量没有被代理到，因此开启了 OpenClash 的 [TUN 模式](https://github.com/vernesong/OpenClash/wiki/常规设置)，开启以后发现更糟了，直接无法上网。搜了一下发现已经有[对应的 Issue](https://github.com/vernesong/OpenClash/issues/1835)，疑似是旁路由和 OpenClash 防火墙设置有冲突，删掉对应的规则即可。

顺便说一句 Apple TV 真的有点香，这个价位，这个性能。

[^1]: https://openwrt.org/toh/xunlong/orange_pi_r1_plus
[^2]: https://github.com/SuLingGG/OpenWrt-Rpi
