---
title: "App 使用体会记录 - macOS 篇"
categories: ['App']
tags: ['macOS', 'App']
date: 2023-12-03T23:44:24+08:00
draft: false
slug: "macos-app-use-experience-record"
typora-root-url: ../../static
---
平时很喜欢倒腾各种 Apple 平台的各种软件，所以体验了很多 iOS/iPadOS/macOS 上的软件，所以计划汇总一下自己的一些简短的使用体验和评价等，希望能在大家后续选择软件的时候起到一些参考和帮助。

由于软件可能较多，不能一次性写完。同时，因为我也在不断体验新的软件，有新的感受，因此这篇博客应该是持续更新的，欢迎常来看看。

## 速览表

| App 名称              | 简短描述                              | 功能限制                           | 订阅形式 | 价格 | 购入时间 | 购入渠道 | 同类型产品                                                   |
| --------------------- | ------------------------------------- | ---------------------------------- | -------- | ---- | -------- | -------- | ------------------------------------------------------------ |
| AIDente               | 电池电量管理                          | 有免费使用功能，但有共功能需要收费 | 买断     | Free |          |          | Battery, BatFi                                               |
| AdGuard               | 广告拦截软件                          |                                    |          |      |          |          |                                                              |
| AltTab                | 增强 macOS App 切换                   |                                    |          |      |          |          | Command-Tab Plus, Contexts,HyperSwitch,                      |
| Bartender 5           | Menubar 管理                          |                                    |          |      |          |          |                                                              |
| Bob                   | 大概是最好用翻译软件                  |                                    |          |      |          |          | EasyDict                                                     |
| BuhoCleaner           | 垃圾清理器/软件卸载器                 |                                    |          |      |          |          | CleanMyMac, Sensei                                           |
| Clash X / Clash X Pro | 代理                                  |                                    |          |      |          |          | Surge, Stash                                                 |
| CleanBuddy            | 键盘锁定清理                          |                                    |          |      |          |          | Cleaner                                                      |
| CleanShot X           | 大概最好用的截图软件                  |                                    |          |      |          |          |                                                              |
| Cleaner               | 键盘屏幕锁定清理                      |                                    |          |      |          |          | CleanBuddy                                                   |
| Dash                  | 文档利器                              |                                    |          |      |          |          |                                                              |
| Dato                  | Menubar 日历软件                      |                                    |          |      |          |          | Fantastical, Itsycal                                         |
| DevUtils              | 开发小工具合集                        |                                    |          |      |          |          |                                                              |
| Downie 4              | 各类视频下载器                        |                                    |          |      |          |          | VDown                                                        |
| HapiGo                | 启动器                                |                                    |          |      |          |          | Alfred, Raycast                                              |
| HazeOver              | 生产力工具，突出重点                  |                                    |          |      |          |          |                                                              |
| Hazel                 | 自动清理集合                          |                                    |          |      |          |          |                                                              |
| JetBrains 全家桶      | 包含 Clion/IDEA 等                    |                                    |          |      |          |          | VSCode, Eclipse                                              |
| Lunar                 | 显示器管理                            |                                    |          |      |          |          | MonitorControl, DisplayBuddy, Better Display                 |
| Maccy                 | 开源免费的剪切板管理工具              |                                    |          |      |          |          | Paste, PasteNow, PastePal                                    |
| Manico                | 快速启动器                            |                                    |          |      |          |          | Command-Tab Plus                                             |
| MimeStram             | Gmail 客户端                          |                                    |          |      |          |          |                                                              |
| MonitorControl        | 开源显示器管理，很够用                |                                    |          |      |          |          | Lunar, DisplayBuddy, Better Display                          |
| NetNewsWire           | 全平台 RSS 阅读器                     |                                    |          |      |          |          |                                                              |
| Obsidian              | 笔记软件                              |                                    |          |      |          |          |                                                              |
| Omnivore              | 稍后读软件                            |                                    |          |      |          |          | Pocket, MarkMark,                                            |
| Only Switch           | 一键完成各种任务                      |                                    |          |      |          |          | One Switch                                                   |
| OpenImageOptim        | 图片压缩                              |                                    |          |      |          |          | TinyPNG                                                      |
| OrbStack              | 容器管理，平替 Docker Desktop         |                                    |          |      |          |          | Docker Desktop, Podman, lima, colima                         |
| Permute 3             | 图片/视频格式转换工具，类似于格式工厂 |                                    |          |      |          |          | HandBrake, Omni Converter, Video Converter X2                |
| Pixea                 | 图片查看器                            |                                    |          |      |          |          | Viso, Picsee                                                 |
| Pixelmator Pro        | 图片编辑器，80% 平替 PhotoShop        |                                    |          |      |          |          | Adobe PhotoShop, Darkroom, Photomator, Capture One, Adobe LightRoom Classic |
| PopClip               | 划词增强                              |                                    |          |      |          |          |                                                              |
| Proxyman              | 大概是舒服的抓包工具，略贵            |                                    |          |      |          |          | Charles                                                      |
| Rectangle             | 分屏软件                              |                                    |          |      |          |          | Magnet, Swish                                                |
| Swish                 | 触控板手势增强                        |                                    |          |      |          |          |                                                              |
| Stats                 | Menubar 状态监控                      |                                    |          |      |          |          | iStats Menu, Sensei                                          |
| Subscriptions         | 订阅管理                              |                                    |          |      |          |          | Pandora on iOS                                               |
| Surge                 | 代理工具                              |                                    |          |      |          |          | Clash X, Stash                                               |
| TinyPNG               | 图片压缩                              |                                    |          |      |          |          | OpenImageOptim                                               |
| Typora                | Markdown 文本编辑器                   |                                    |          |      |          |          | Obsidian                                                     |
| Upscayl               | AI 图片超分                           |                                    |          |      |          |          | AI Photo Enhancer by Pictura, Pixelmator Pro                 |
| Viso 6                | 图片查看器                            |                                    |          |      |          |          | Pixea, Xee                                                   |
| Zotero                | 文献管理，就是界面一般                |                                    |          |      |          |          |                                                              |
| coconutBattery        | iPhone/iPad/Mac 电池信息查看          |                                    |          |      |          |          |                                                              |
| iRightMenu            | 右键增强                              |                                    |          |      |          |          | 超级右键, iMouse,                                            |
| iTerm2                | 终端模拟器                            |                                    |          |      |          |          | Alacritty, Warp, Wezterm, Terminal                           |

## 详细信息

### AIDente

+ 介绍：可以限制充电上限，据称电池电量维持在 70-80% 是比较健康的，不适宜过高。

+ 目前是否在用：是

+ 收费形式：139/3设备

+ 评价：好用，界面也挺好看的

+ 同类型产品：Battery, BatFi

+ 软件快照：

  ![Aldente-1](/images/macos-app-use-experience-record/Aldente-1.png)

TODO...
