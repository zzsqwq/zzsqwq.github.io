---
title: "一个 Javascript 中异步的小技巧"
categories: [ "Javascript" ]
tags: [ "Javascript","异步" ]
draft: false
slug: "223"
date: "2022-01-19 00:24:00"
---

## 前言

最近看了一些 js 有关的知识，其中令我这种初学者感到很头疼的一个问题就是异步问题。

今天就我碰到的一个小问题详解一个关于异步的小技巧。

## 背景

我在程序中需要鉴权，来判断一个用户是**普通用户**还是**管理员**，针对不同的用户渲染不同的页面。

每个用户具有唯一的 ID，因此我只需要将管理员的 ID 放到数据库，然后加载程序的时候一一比对即可。

但是我的主程序初始化与页面的初始化是异步的，管理员的判断逻辑我放在了主程序初始化中，这也就意味着很可能我页面在加载时，主程序还没有判断完用户的身份。

每个人默认不是管理员，因此这就会导致一个问题——**管理员可能也会显示成普通用户的页面**，因为渲染页面的时候程序还不知道这个用户是管理员。

## 方案

这个方案其实是我从官方的代码上学到的，其实就结合代码给大家讲一下。

官方这里是在获取用户的 userInfo 时用到的。

**主程序初始化部分代码**：

这里主程序的逻辑就是在 `getSetting` 调用成功后，判断是否已经获得过 userInfo ，如果已经获得过，则可以直接调用 `getUserInfo` 函数获取值，赋值成一个主程序的全局变量，所有页面都可以用。

接下来就是一个比较奇怪的点了

可以看到官方的注释为

> 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
>
> 所以此处加入 callback 以防止这种情况

这里判断了主程序内是否含有 `userInfoReadyCallback` 这个函数，如果有的话就执行。

```javascript
qq.getSetting({
            success: res => {
                if (res.authSetting['scope.userInfo']) {
                    // 已经授权，可以直接调用 getUserInfo 获取头像昵称，不会弹框
                    qq.getUserInfo({
                        success: res => {
                            // 可以将 res 发送给后台解码出 unionId
                            this.globalData.userInfo = res.userInfo

                            // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
                            // 所以此处加入 callback 以防止这种情况
                            if (this.userInfoReadyCallback) {
                                this.userInfoReadyCallback(res)
                            }
                        }
                    })
                }
            }
        })
```

**页面初始化部分代码：**

页面初始化部分的逻辑首先判断全局变量中是否包含 `userInfo` 这个这个变量，我们从上文得知，如果我们在此段程序执行前主程序已经执行完毕，那么这里是包含 `userInfo` 这个函数的，我们直接赋值给当前的环境变量。

而如果没有 `userInfo` 没有定义，也就是**主程序还未完成变量的初始化**，这就出现了我们之前提到的问题，页面加载时主程序未初始化完。

定义了 `userInfoReadyCallback` 函数，接受 res 参数，进行赋值。

```javascript
if (app.globalData.userInfo) {
            this.setData({
                userInfo: app.globalData.userInfo,
                hasUserInfo: true
            })
        } else if (this.data.canIUse) {
            // 由于 getUserInfo 是网络请求，可能会在 Page.onLoad 之后才返回
            // 所以此处加入 callback 以防止这种情况
            app.userInfoReadyCallback = res => {
                this.setData({
                    userInfo: res.userInfo,
                    hasUserInfo: true
                })
            }
        } else { 
//......
```

读完上面的程序大家应该已经可以搞懂了，其实类似于设置了两个标志(flag)。

一个标志是全局变量 `userInfo`、一个是全局函数 `userInfoReadyCallback` 。

如果 `userInfo` 已经定义，说明页面初始化前页面主程序变量已初始化完毕，渲染不会出问题。

而如果 `userInfo` 未定义，说明与上面相反，则在页面中定义`userInfoReadyCallback` 函数，后续主程序初始化到最后得知该函数已定义，则调用其来进行初始化。

这样就总能够保证局部 `userInfo` 这个变量的取值总是正确的，也即页面总是渲染正确的！

## 后记

这个问题应该总是可以解决两个页面同时加载但却存在变量依赖的问题。

大家如果有什么更加优秀的解决方案也希望不吝赐教。