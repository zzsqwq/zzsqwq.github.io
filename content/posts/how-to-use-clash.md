---
title: "如何在 Linux 上优雅的使用 Clash？"
categories: ["代理"]
tags: ["clash","proxy"]
draft: false
slug: "how-to-use-clash-on-linux"
date: "2023-08-07T02:26:22+08:00"
---

{{< notice tip >}} 此方式推荐在纯终端（无图形化界面）环境下使用，有图形化界面的情况下推荐使用 [Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg/releases) 或 [Clash Verge](https://github.com/zzzgydi/clash-verge)，相关博文可见—[Linux 上 Clash For Windows 如何设置开机自启与桌面图标](https://blog.zzsqwq.cn/posts/clash-for-windows-on-linux/)。{{< /notice >}}

## 为什么选择 Clash？

目前 Clash 由于其基于规则分流的强大代理功能，已经成为主流的代理内核，在各个平台都有对应的客户端。如 Windows 端的 Clash For Windows(CFW)，MacOS 端的 ClashX，Android 端的 Clash For Android，IOS 端 Choc。

那么我们如何能够在 Linux 上优雅的使用 Clash 呢？

## 一、基于 Docker 的实现

[Docker](https://www.docker.com/) 是一个开源的应用容器引擎，开发者可以把包和依赖等集成到一个容器中，并打包成镜像发布。用户可以直接拉取镜像并创建容器，而不会污染本地的环境，**十分的好用**。

我现在在使用一个软件的时候一般都是有 docker 实现会优先使用 docker，配合 [docker-compose](https://docs.docker.com/compose/) 管理真的非常的方便。因此我们优先考虑使用 docker 来配置 clash 代理。

### 1.1 安装 docker

大家可以参考 [官网安装指南](https://docs.docker.com/get-docker/) 来在 Linux 安装 docker，就不详细赘述了。

顺便可以参考 [docker-compose 安装指南](https://docs.docker.com/compose/install/) 来安装 docker-compose。

安装结束后，使用 `docker --version` 以及 `docker-compose --version` 进行验证，输出如下进行下一步。

```bash
❯ docker --version        
Docker version 20.10.14, build a224086

❯ docker-compose --version
docker-compose version 1.29.2, build 5becea4c
```

### 1.2 创建 docker-compose 文件

首先打开一个新的终端，进入根目录，运行如下指令，创建文件夹存放 docker-compose 配置文件。这里是在当前用户家目录下创建了 Services 文件夹，并在其中创建了 clash 文件夹。

```bash
❯ mkdir -p ~/Services/clash
```

然后进入上述文件夹，并使用文本编辑器创建 `docker-compose.yml` 文件

```bash
~
❯ cd ~/Services/clash     

~/Services/clash
❯ vim docker-compose.yml
```

在文件中填入下述内容，该文件参考[官方文档](https://github.com/Dreamacro/clash/wiki/clash-as-a-daemon#docker)，并进行了一定的改动

```bash
version: '3'
services:
  clash:
    # ghcr.io/dreamacro/clash
    # ghcr.io/dreamacro/clash-premium
    # dreamacro/clash
    # dreamacro/clash-premium
    image: dreamacro/clash
    container_name: clash
    volumes:
      - ./config.yaml:/root/.config/clash/config.yaml
      - ./Country.mmdb:/root/.config/clash/Country.mmdb
      - ./ui:/ui # dashboard volume
    ports:
      - "7890:7890"
      - "7891:7891"
      - "9090:9090" # external controller (Restful API)
    # # TUN
    # cap_add:
    #   - NET_ADMIN
    # devices:
    #   - /dev/net/tun
    restart: unless-stopped
    network_mode: "bridge" # or "host" on Linux
```

我们映射了三个文件到容器内部，分别是代理配置文件 `config.yaml` ，全球IP库文件 `Country.mmdb` ，控制面板静态源码 `ui` 文件夹。

然后映射了三个端口，上述三个端口分别对应了 `config.yaml` 文件中的 `port`、`socks-port`、`external-controller`。分别用于 http/https 流量代理，socks 流量代理，以及外部控制。

### 1.3 下载对应的文件

首先是 config.yaml 文件，这个就是下载你的配置文件，将 `$your-proxy-url` 替换为对应的订阅链接即可。

```shell
~/Services/clash
❯ cd ~/Services/clash #下述所有操作都在该文件夹执行

~/Services/clash
❯ wget -O config.yaml $your-proxy-url
```

然后下载 `Country.mmdb` 文件，虽然这个文件在没有的时候会自动下载，但是一般速度较慢，还是手动下载比较好，你可以在这里找到对应的文件 [Dreamacro/maxmid-geoip/releases](https://github.com/Dreamacro/maxmind-geoip/releases) 。

```shell
~/Services/clash
❯ wget https://github.com/Dreamacro/maxmind-geoip/releases/download/20220412/Country.mmdb
```

然后下载对应的 ui 控制面板，目前有两款，分别是 clash 原作者的 [clash-dashboard](https://github.com/Dreamacro/clash-dashboard) 以及另一位大佬的 [yacd-dashboard](https://github.com/haishanh/yacd) ，大家自选其一即可，我常用的是 `clash-dashboard`。

**Ps：这一步算是可有可无，大家可以直接访问对应的链接**

+ clash-dashboard：http://clash.razord.top/
+ yacd-dashboard：http://yacd.haishan.me/

填入对应的 IP、端口、密钥进行控制，**如果上述需求已经足够，就无需配置后续的本地面板**。

```shell
~/Services/clash
❯ git clone -b gh-pages https://github.com/Dreamacro/clash-dashboard ui
```

这样就克隆 [clash-dashboard](https://github.com/Dreamacro/clash-dashboard) 仓库的 gh-pages 分支到了 ui 文件夹。

然后配置一下我们的 `config.yaml` 文件，确保下面四项如下：

```shell
port: 7890
socks-port: 7891
external-controller: :9090
external-ui: /ui
```

这里的 `port` 是 http/https 代理端口，`socks-port` 是 socks 流量代理端口，`external-controller` 是外部控制端口，用于面板控制，`external-ui` 是本地控制页面的源码，确保 `localhost:external-controller/ui` 页面可以控制代理。

顺便说一下，这里其实严格意义上不是必须和我一样，只要和前面的 `docker-compose.yml` 文件中的端口映射对应起来即可，但是如果你对 Docker 及 Clash 不够了解，推荐和我选择一样的配置。

### 1.4 启动容器

在该目录下启动容器

```shell
~/Services/clash
❯ docker-compose up -d
```

后续可以通过 `docker ps` 查看容器状态

```shell
~/Services/clash
❯ docker ps               
CONTAINER ID   IMAGE             COMMAND    CREATED          STATUS          PORTS                                                                                                      NAMES
304c4934e528   dreamacro/clash   "/clash"   53 minutes ago   Up 52 minutes   0.0.0.0:7890-7891->7890-7891/tcp, :::7890-7891->7890-7891/tcp, 0.0.0.0:9090->9090/tcp, :::9090->9090/tcp   clash
```

可以通过在该目录下执行 `docker-compose logs` ，或在任何位置执行 `docker logs clash` 查看容器日志

```shell
~/Services/clash
❯ docker-compose logs | tail -n 20 #查看最近20条log

~
❯ docker logs clash | tail -n 30 #查看最近30条
```

 关闭容器可以使用 `docker-compose down`，即可停止并移除该容器，不留任何残余（~~干净又卫生啊兄弟们~~）。

```shell
~/Services/clash
❯ docker-compose down 
Stopping clash ... done
Removing clash ... done
```

### 1.5 控制代理

+ clash-dashboard：http://clash.razord.top/
+ yacd-dashboard：http://yacd.haishan.me/

后续大家可以通过访问上述的两个公开链接，或者本地的 `localhost:9090/ui`（必须配置了 `external-ui` 参数才可使用）来对代理进行控制。 **其中 Host 为你的服务器 IP 地址或者域名，端口为 9090，密码默认为空。**

![clash-dashboard 界面][1]

![yacd-dashboard 界面][2]

## 二、基于 Systemd 的实现

Systemd 是 Linux 系统工具，用来启动[守护进程](https://www.ruanyifeng.com/blog/2016/02/linux-daemon.html)，已成为大多数发行版的标准配置。

大家可以通过下面阮一峰老师的教程[^1]来学习 Systemd 的一些基础知识。

### 2.1 下载 Clash 内核

首先下载 clash 内核并且移动到用户可执行目录下，你可以在这里找到最新的内核 [clash-core](https://github.com/Dreamacro/clash/releases)。

```shell
~
❯ cd ~/Downloads

~/Downloads
❯ wget https://github.com/Dreamacro/clash/releases/download/v1.10.0/clash-linux-amd64-v1.10.0.gz # 如果速度较慢，可以添加 https://ghproxy.com/ 形成 https://ghproxy.com/https://github.com/Dreamacro/clash/releases/download/v1.10.0/clash-linux-amd64-v1.10.0.gz 式的链接进行加速

# 解压
~/Downloads
❯ gunzip clash-linux-amd64-v1.10.0.gz

# 移动到 /usr/local/bin 目录
~/Downloads
❯ sudo mv clash-linux-amd64-v1.10.0 /usr/local/bin/clash
```

### 2.2 下载各类配置文件

这里跟上面的 **1.3 下载对应的文件** 非常的相似，大家可以参考上面的进行，不过可以都统一下载到 `/opt/clash` 文件夹中，包括 `config.yaml`、 `Country.mmdb` 文件及 `ui` 文件夹（如果不需要本地面板，则不需要配置 ui）。

下面是创建文件夹等过程示例，下载配置文件请参考 1.3 节进行

```shell
~/Downloads
❯ sudo mkdir -p /opt/clash

~/Downloads
❯ cd /opt/clash

/opt/clash
❯ wget -O config.yaml ..............

# 接下来下载对应的文件
```

最后结构目录应该如下所示，ui 为文件夹

```shell
/opt/clash
❯ ls -la
total 5508
drwxr-xr-x 3 root root    4096 4月  23 20:30 .
drwxr-xr-x 9 root root    4096 4月  22 09:02 ..
-rw-r--r-- 1 root root  177047 4月  22 09:04 config.yaml
-rw-r--r-- 1 root root 5446245 4月  12 08:56 Country.mmdb
drwxr-xr-x 4 root root    4096 4月  22 08:40 ui
```

### 2.3 配置 clash.service 守护进程文件

```shell
~/Downloads
❯ sudo vim /etc/systemd/system/clash.service
```

运行上述命令，并在文件中写入下列内容，这里参考了官网的文档[^2]

```shell
[Unit]
Description=Clash daemon, A rule-based proxy in Go.
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/clash -d /opt/clash/
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

大家如果对上述内容有疑问，可以参考阮一峰老师的教程。

### 2.4 修改配置文件

如 **1.3 下载对应的文件** 所示，要确保配置文件中的下面四个参数对应如下：

```shell
port: 7890
socks-port: 7891
external-controller: :9090
external-ui: /opt/clash/ui
```

具体参数的作用上面已经讲过了。

### 2.5 启动并设置开机启动

**启动 Clash service 守护进程**

```shell
~/Services/clash
❯ sudo systemctl start clash
```

**设置为开机自启动**

```shell
~/Services/clash
❯ sudo systemctl enable clash
Created symlink /etc/systemd/system/multi-user.target.wants/clash.service → /etc/systemd/system/clash.service.
```

**查看 Clash 进程状态**，确保下面的 `Active` 为 `active(running)` 即正常。如果有遇到问题可以评论或者通过搜索引擎。

```shell
~/Services/clash
❯ sudo systemctl status clash
● clash.service - Clash daemon, A rule-based proxy in Go.
     Loaded: loaded (/etc/systemd/system/clash.service; disabled; vendor preset>
     Active: active (running) since Sat 2022-04-23 21:49:39 CST; 43s ago
   Main PID: 120629 (clash)
      Tasks: 13 (limit: 18860)
     Memory: 16.1M
     CGroup: /system.slice/clash.service
             └─120629 /usr/local/bin/clash -d /opt/clash/
```

**查看详细日志**，可以使用 `journalctl`，所有 Systemd 服务的日志都可以通过这个查看

```shell
~/Services/clash
❯ sudo journalctl -u clash     
-- Logs begin at Thu 2022-04-14 23:14:57 CST, end at Sat 2022-04-23 21:53:15 CS>
4月 15 01:23:14 zs-XiaoXinPro systemd[1]: /etc/systemd/system/clash.service:5: >
4月 15 01:23:14 zs-XiaoXinPro systemd[1]: Started clash daemon.
```

**取消开机自启动**

```shell
~/Services/clash
❯ sudo systemctl disable clash
Removed /etc/systemd/system/multi-user.target.wants/clash.service.
```

**停止 Clash 服务守护进程**

```shell
~/Services/clash
❯ sudo systemctl stop clash
```

## 三、测试代理

完成后上述两种方式之一就可以使用代理了，其中 http 代理端口为 7890、socks 代理端口为 7891（有时 7890 也是混合端口，两种都可以。）

接下来浏览器代理可以使用 [Proxy SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif?hl=zh-CN) 插件，Linux 命令行中的代理推荐使用 [proxychains](https://github.com/haad/proxychains)，或者可以设置环境变量。但是环境变量的方式有的流量无法代理到，例如 apt 需要单独设置才可以，而 proxychains 可以解决。但是 proxychains 也有不足之处，无法代理 Go 编译程序，想要代理 Go 程序可以使用 [graftcp](https://github.com/hmgle/graftcp)。

### Proxychains 

首先安装

```bash
$ sudo apt update
$ sudo apt install proxychains-ng 
```

接下来设置相关配置文件

```bash
$ sudo vim /etc/proxychains4.conf 
```

注释掉其他无关的，最下方添加我们设置的代理

```bash
[ProxyList]
http 127.0.0.1 7890
socks5  127.0.0.1 7891
```

接下来 :wq 保存退出即可。

### 环境变量

只需要设置如下环境变量即可：

```bash
export proxy_url="http://127.0.0.1:7890"
export http_proxy=$proxy_url
export https_proxy=$proxy_url
export ftp_proxy=$proxy_url
export no_proxy="localhost, 127.0.0.1, ::1"
```

如果想要持久化，只需要编辑 `.bashrc` 或者 `.zshrc`（如果你使用的是 zsh），将上述命令加入到最后即可。

### 测试

```bash
$ proxychains4 wget google.com ## 如果是环境变量的方式，直接 wget google.com 即可。
ubuntu@VM-16-11-ubuntu:~$ proxychains4 wget google.com
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.14
--2023-08-07 02:15:20--  http://google.com/
Resolving google.com (google.com)... 224.0.0.1
Connecting to google.com (google.com)|224.0.0.1|:80... [proxychains] Strict chain  ...  127.0.0.1:7890  ...  127.0.0.1:7891  ...  google.com:80  ...  OK
connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: http://www.google.com/ [following]
--2023-08-07 02:15:20--  http://www.google.com/
Resolving www.google.com (www.google.com)... 224.0.0.2
Connecting to www.google.com (www.google.com)|224.0.0.2|:80... [proxychains] Strict chain  ...  127.0.0.1:7890  ...  127.0.0.1:7891  ...  www.google.com:80  ...  OK
connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘index.html’

index.html                               [ <=>                                                                  ]  18.87K  --.-KB/s    in 0s

2023-08-07 02:15:20 (204 MB/s) - ‘index.html’ saved [19321]
```

输出如上即正确，如果想要不输出中间的 log，可以使用 `proxychains4 -q`。

不要认为无法 ping 通 Google 就是代理有问题，此种代理方式就是无法 ping 通。

如果还有问题，可以使用 `docker-compose logs -f ` 命令查看容器的相关日志，看是否有请求到达。同时可以检查配置文件是否正确，如果不确定可以通过 [ACL4SSR 在线订阅转换 (acl4ssr-sub.github.io)](https://acl4ssr-sub.github.io/) 转换下试试（非推广，无法保证安全性，请使用前自行考量）。如果还是无法解决，可以留言评论探讨。

[^1]: [Systemd 入门教程：命令篇](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html) 以及 [Systemd 入门教程：实战篇](https://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)
[^2]: https://github.com/Dreamacro/clash/wiki/clash-as-a-daemon

[1]: /images/how-to-use-clash-on-linux/clash-dashboard-demo.png
[2]: /images/how-to-use-clash-on-linux/yacd-dashboard-demo.png
