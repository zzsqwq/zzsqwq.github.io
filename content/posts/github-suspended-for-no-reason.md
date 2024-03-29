---
title: "记一次 GitHub 账号突然被 suspended 的经历"
date: 2023-11-12T23:36:41+08:00
categories: ["经验分享"]
tags: ["Github","Suspended"]
slug: "github-suspended-for-no-reason"
draft: false
typora-root-url: ../../static
---

## TL;DR

我在 2023.11.07 凌晨被 GitHub 封禁了账号，GitHub 账号被 suspended 后会在 GitHub 上完全除名（只有自建创建的组织不会消失）。

被封禁后我首先通过 Google 查找了相关的文章。

根据博主 [@phith0n](https://github.com/phith0n) 的指引首先通过 API：`https://api.github.com/users/[username]/starred` 备份了自己 star 过的项目，同时通过 [Can't sign-in form](https://support.github.com/contact/cannot_sign_in) 来在无法登陆的情况下发起 GitHub Support 工单来询问/请求解封/询问原因等，一般在两天内收到了回复。最终账号顺利恢复。

我此次被封禁的原因是由于管理的组织内部有成员被盗号，在组织内创建了违法仓库，因此管理员和被盗号的成员都被封禁了，但后续都被解除了封禁。同时也有可能因为创建违反 [DMCA 条款](https://en.wikipedia.org/wiki/Digital_Millennium_Copyright_Act) 的仓库、fork 违反 DMCA 条款的仓库、贩卖学生/教师教育包等原因被封禁。

## 背景 

大约在 2023.11.07 凌晨 00:30 左右，在 GitHub 上看到了一个不错的项目想要 Star，被提示没有登陆。当时我还在想：“怎么这次 session 过期的这么快？” 就当我边疑惑边重新登录后，突然收到了下图的提示[我的账号](https://github.com/zzsqwq)「Account suspended」：

<div align="center"><img src="/images/github-suspended-for-no-reason/account-suspended-hint.jpg" style="zoom:50%;" /></div>

确认无法登录后，查看邮箱没有任何相关通知。同时我查看了其他内容是否还存在，最后综合表现如下：

1. 没有任何邮件/信息通知被封禁及更加具体的封禁原因，只告诉违反了 ToS
2. 个人页面（https://github.com/zzsqwq）无法访问，访问显示 404
3. 提的相关 Issue 全部被删除（隐藏），无法找到
4. 个人创建的所有项目，访问全部 404，也无法拉取
5. 自己参与的项目，被除名
6. 自己加入的组织，被除名
7. ~~[自己创建的组织](https://github.com/nwpusr-vision-team)，访问 404~~

简单说就是跟这个账号从来没存在过一样，除了仍可以登陆，但是显示 suspended。接下来开始尝试找办法恢复。

## 恢复经过

### 试图查找封禁原因

首先看提示上显示违反了 [Github Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service)，快速的看了一眼 ToS 没发现我有什么明显违规的地方。

通过搜索引擎搜索 「Github 账号 suspended」相关信息，找到了两个比较强相关的帖子：

+ [分享下我 GitHub 被封的经历](https://www.leavesongs.com/THINK/my-github-account-used-to-be-suspended.html)
+ [GitHub 无预警突然封号](https://www.v2ex.com/t/836086)

其中第一个是因为 fork 了违反 DMCA 的仓库而被封禁，第二个看起来是因为购买了违规的教师包而被封禁。

首先我是使用正规身份申请的学生教育包，第二个博主的情况可以排除。关于第一个，我仔细想了一下我过去几天的行为：由于过去几天大量 Clash 相关仓库被删除和停止维护，我 fork 了五个与 Clash 有关的仓库，但是理论上 Clash 相关的仓库是不存在违反 DMCA 这一说的，因此第一个博主的情况也可以被排除。

**同时第一个博主提到**，我们可以通过 API：`https://api.github.com/users/[username]/starred` 来找到之前 star 过的仓库，我试了一下确实，遂备份了一份 json 文件。同时查看了关于关注和被关注相关的 API：`https://api.github.com/users/[username]/followers` 和  `https://api.github.com/users/[username]/following`，均为空，看来只能找到 star 过的仓库。

**但是有一个比较神奇的点是**：第一个博主提到：「但是我创建的 Group 还是好的，没有受影响。」。但是在我账号上，我唯一创建的组织 [nwpusr-vision-team](https://github.com/nwpusr-vision-team) 访问时也显示 404，和这个博主的情况不符，但暂时不明是什么原因。

### 联系 GitHub 寻求帮助

于是经过一系列查找，我在 GitHub 上使用新的邮箱注册了一个新的账号（旧的邮箱无法注册新账号），并通过 [Github Support](https://support.github.com/) 界面发起了一个工单说明我的情况。当时是大概凌晨一点多提交的工单，2023.11.07 下午五点多收到了回复，告知我必须使用原邮箱来联系 Github Support 支持才可以处理我的原账号，可以通过 [Can't sign-in form](https://support.github.com/contact/cannot_sign_in) 来在无法登录的情况下提交相关工单（但是似乎直接给 Github 发邮件也可以成功创建工单，不确定）。

同时在这一天的下午，我也得知了有另外两位认识的同学也跟我一模一样在没有任何通知的情况下被封禁，具体的表现也和我一样。而我们之间的联系就是我们同属于一个组织：[nwpusr-vision-team](https://github.com/nwpusr-vision-team) 。至此我推断出了一个大致的原因 —— 我创建的这个 Github 组织出了问题，导致我们三个都受到了牵连，但是具体的什么原因还未知，因为这个组织从我创建后几乎就没用过，里面也只有一个 [README 一样的仓库](https://github.com/nwpusr-vision-team/team-introduction)，实在是想不明白有什么封禁的理由。

一直等到第三天即 2023.11.09 的早上9点多，我收到了一封关于密码被重置的邮件，于是我查看了我的 [GitHub个人主页](https://github.com/zzsqwq) 发现已经不再是 404 了。于是我按照提示重置了密码后，顺利的登录了 Github，同时也收到了 GitHub Support 的回复，全文如下：

> Hello Zhan,
>
> Thanks for contacting GitHub Support on the email address associated with your `zzsqwq` account.
>
> We recently detected suspicious activity on your account and applied restrictions out of an abundance of caution while we investigated what took place.
>
> To secure your account, we have forced a password reset. If you haven’t already, please reset your password.
>
> I’ve sent you a separate email just now with a link to complete this process. Please note that the link will expire in 24 hours. Alternatively, visit the following to request a password reset token:
>
> [https://github.com/password_reset](https://github.com/password_reset.)
>
> To protect your account from unauthorized access, please choose a strong and unique password for your account. We have a help article with some recommendations here:
>
> https://docs.github.com/authentication/keeping-your-account-and-data-secure/creating-a-strong-password
>
> As an added precaution, we also recommend reviewing your security log and reverting any changes you don’t recognize.
>
> For more information, read [Reviewing your security log](https://docs.github.com/authentication/keeping-your-account-and-data-secure/reviewing-your-security-log) in the GitHub Docs.
>
> Additionally, we also recommend double checking your stars and removing any that weren’t added by you:
>
> https://github.com/stars
>
> I hope this clears things up. If you have any further questions, please let us know.
>
> Kind regards,
> Pip,
> GitHub Support

大意就是 GitHub 最近在我的帐户上检测到可疑活动，并在调查发生的事情的同时出于谨慎考虑对我的账号施加了限制。现在已经把我的账号解封了，但是需要重置密码后才可以继续使用。

### 后续处理

登录账号后，我发现组织 nwpusr-vision-team 仍存在限制，提示如下：

<img src="/images/github-suspended-for-no-reason/organization-flagged.png" style="zoom:50%;" />

由于被对外隐藏了，因此我自己可以访问这个组织，但是如果不登录账号查看这个组织仍是 404 的。

通过对组织进行查看，发现组织内多了一个新的仓库，并且是违规仓库，是 fork 了一个 [仓库](https://github.com/RoboMaster/RoboRTS) 并更新了 README 更新成违规信息。

<img src="/images/github-suspended-for-no-reason/hacked-repo.jpg" style="zoom:50%;" />

这下我大概推测出了封号原因：首先下图是我们目前仓库成员图，红框内的是这次被封禁的人，从上到下依次是被盗号且创建违法仓库的同学、另一位管理员同学和我。应该由于那位同学被盗号并且在组织内创建了违法仓库，但是身为管理员的我们两个人在有管理权的情况下没有及时将仓库删除，因此 GitHub 认为我们三个人都可能账号存在风险，因此一起被禁了。随后进一步的调查发现这三个账号都已经没什么异常，因此就直接解除了封禁。

<img src="/images/github-suspended-for-no-reason/organization-people.png" style="zoom:50%;" />

随后我将组织内的违法仓库删除并反馈给 Github Support 后，组织状态也恢复了正常。

随后我又就被封号原因这个问题，和 Github Support 人员展开了一些沟通，但是他们只是一直在说「GitHub 在我的帐户上检测到可疑活动，并在调查发生的事情的同时出于谨慎考虑对我的账号施加了限制，由于这个过程是他们检测工具自动进行的，恕他们无法告知更准确的原因」，最后仍是无法得知我推断的是否正确，但是应该除此之外没什么其他的原因了，由于我 GitHub 开启了 2FA 同时没泄露任何 token，理论上不会被有被异地登陆的可能。

> Our security team has recently been investigating suspicious activity and account hijacking, and we were concerned that your account may have been affected. Out of an abundance of caution, restrictions were placed on your account as part of our attempts to combat this campaign.
>
> We use a number of detection methods to find abuse on GitHub, but I’m afraid I’m not at liberty to discuss our internal tooling.

## 后记

整件事情看下来其实 GitHub 的处理有点离谱，仅仅是检测到账号可能存在异常活动，就在**没有任何通知且不告知详细缘由**的情况下封禁了账号，同时无法访问任何仓库/Issue，目前来看恢复账号也需要个至少两天，可能会耽误很多事情。

我也就这件事情进行了反馈：

> Lastly, I believe the way GitHub officially handled this situation was quite unreasonable. Our accounts were banned without any email notification, making it impossible to access repos, followers, following, stars, issues (all showing 404), and without stating a specific reason. For instance, as mentioned in the previous reply: "We recently detected suspicious activity on your account and applied restrictions out of an abundance of caution while we investigated what took place." I think if you were to conduct an investigation, you could consider first imposing restrictions on the account (such as only allowing the repository to be pulled without any write operations on the account/repository) and notify us by email: "We have detected unusual activity and have restricted your account for safety reasons." This would seem more reasonable and wouldn't leave us confused. Please seriously consider the workflow for the future; this will make everyone love GitHub more. Thank you!

他们的一些回复如下（分多封邮件进行交流，不同邮件间使用分割线分割）：

> Thanks for your reply and your feedback, I appreciate you taking the time to share it with me and I will make sure to share it with the relevant team.
>
> ---
>
> We restricted your account as we had reasons to believe it may have been compromised in the aforementioned campaign. However, in your specific case, further review suggests that your personal account was unlikely to have been affected. I do apologize for the inconvenience, but hope you understand our need to prioritize account security.
>
> ---
>
> I understand that having your account restricted unexpectedly can be a frustrating experience, and I appreciate your feedback on receiving notification. I have shared your feedback internally.

希望 GitHub 后续可以改进这个流程。

---

Ps：这次导致封号的违规仓库居然还是 fork 的 [RoboRTS](https://github.com/RoboMaster/RoboRTS) ，就连这个都跟 RM 有关，有点离谱。

