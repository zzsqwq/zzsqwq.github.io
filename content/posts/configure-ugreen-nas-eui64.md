---
title: "绿联 NAS 配置基于 EUI64 的 SLAAC IPv6 地址"
categories: ["NAS"]
tags: ["绿联", "UGREEN", "NAS"]
draft: false
slug: "ugreen-nas-configure-eui64-ipv6"
date: "2024-12-05T00:08:00+08:00"
---

最近在玩 PT 站，家里没有公网 IPv4 地址但有公网 IPv6 地址，绿联 NAS 上挂的 qBittorrent 需要靠 IPv6 刷上传量。

但是一般来说 IPv6 的地址都是随机生成的，不好在 OpenWrt 配置防火墙规则放行对应设备地址和端口，如果全部放行又过于危险，所以需要找到一种可以固定放行对应的设备地址和端口的方法。

参考 [不良林的 IPv6 配置视频](https://www.youtube.com/watch?v=klQ6JbZEeJ0&list=PL5TbbtexT8T293fBi4i5MOf4OjdLQ3hUa&index=6) 可以找到一种组合：

1. SLAAC 的方式下发 IPv6 地址
2. 设备端配置 EUI64 模式生成后缀 
3. 防火墙通行规则匹配特定后缀放行[^1]

第一点和第三点在视频中讲的都很清楚，主要是第二点，其中 Windows、Mac、和基于 NetworkManager 的 Linux 都可以很方便的在网上找到教程更改，但是绿联的 NAS 使用的 UGOS Pro 系统并不基于 NetworkManager，需要配置 systemd-networkd，具体步骤如下：

首先配置绿联 NAS 打开 SSH 端口，然后通过 ssh 用具有管理权的帐号登陆到终端

![open-ssh](/images/configure-ugreen-nas-eui64/open-ssh.png)

编辑配置网络文件，文件放到 `/etc/systemd/network/` 下，注意你使用的 LAN 口，如果是 LAN1 的话接口对应 `eth0`，如果是 LAN2 的话接口对应 `eth1`，我这里用的是 LAN1

![lan1](/images/configure-ugreen-nas-eui64/lan1.png)

在此目录下创建 `10-eth0.network` 文件，使用 nano 或 vim 编辑文件，内容如下

```bash
[Match]
Name=eth0  # 替换为你的网络接口名称

[Network]
IPv6AcceptRA=yes  # 接受路由通告

[IPv6AcceptRA]
UseAutonomousPrefix=yes
UseTemporaryAddress=no

[Link]
IPv6Token=::eui64  # 设置为 EUI-64 地址生成方式
```

保存后重启对应服务即可

```bash
sudo systemctl restart systemd-networkd
```

然后通过 `ip addr show eth0` 可以查看到已经通过 eui64 方式生成 IPv6 地址后缀

![success](/images/configure-ugreen-nas-eui64/eui64.png)



---

补充：

后续使用发现绿联每次启动的时候并不会遵守 systemd-networkd 的这个设定，所以还是会自动生成一个 stable-privacy 地址，因此需要每次开启重启一下 systemd-networkd。

绿联 NAS 的 rc.local 文件是自动生成的，所以不建议在里面修改会被自动覆盖，使用 crontab 在 root 用户下添加重启任务即可。

首先 `sudo -i` 切换到 root 用户，然后使用 `crontab -e` 打开定时任务编辑文件，向其中添加：

```bash
@reboot systemctl restart systemd-networkd
```

然后重启系统验证只存在 eui64 地址

[^1]:https://www.v2ex.com/t/1004926
