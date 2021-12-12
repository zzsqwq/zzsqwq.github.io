---
title: "记一次博客迁移记录"
categories: [ "杂谈" ]
tags: [ "杂谈" ]
draft: false
slug: "221"
date: "2021-12-13 02:59:00"
---

## 前言

更换博客系统的想法已经萌生很久了，一个是感觉 Handsome 这个主题有点看腻了，但是在 Typecho 中好似已经没有更好的博客主题可选择了。

有一个看起来貌似很不错，主题名叫 maupassant。效果如下图所示，顺附链接

[pagecho/maupassant(github.com)](https://github.com/pagecho/maupassant)

![maupassant](https://camo.githubusercontent.com/6f925dcec164e50cad950577db00910d3821d388e3f018d4b7d900cc2ce081c0/68747470733a2f2f6464796465672e6279333330322e6c69766566696c6573746f72652e636f6d2f793270315a67484552346549466145486877616639364d765a48345f694c75664549446a376f38616344674931475846447450492d65524167766f6b466f52396972627a373338674d6d57635f4e3779657847367568423144636d656c623063586738486578706941645a3548512f6d2e706e67)

不过一直没有下定决心更换，后来也尝试过使用 WordPress，又觉得 WordPress 体量有点太大了。

每天逛 Github 的时候看到了很多很不错的静态博客主题，那天看到一个学长[分析学校校园网的博客](https://zincnode.com/posts/campusnetwork/)，第一眼就感觉很不错，找寻了一下发现博客基于 [Hugo](https://gohugo.io/)，主题是 [hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)。

于是顺着寻找了一下，发现 Hugo 中很多博客主题非常的不错。此外，Hugo 相比于 Hexo 也有很多优点：博客构建速度快，基于模板的概念组织内容，环境配置容易，在 Ubuntu 下一行命令即可，而 Hexo 依赖于 Node.js，体量稍微有点大。

于是决定把博客迁移到 Hugo，并且采用主题 [Coder](https://github.com/luizdepra/hugo-coder) 。

## 过程

### Typecho 文章导出

这里采用了 lizheming 大大的迁移插件：[lizheming/typecho-export-hugo](https://github.com/lizheming/typecho-export-hugo)

**具体导出为 zip 的时候可能会提示损坏，这样的话可以直接去服务器 `\tmp\Export2Hugo` 下面打包。**

### 安装 hugo

过程基于 Windows 平台，很简单，在 [这里](https://github.com/gohugoio/hugo/releases/tag/v0.90.1) 下载 hugo 最新的 release 版本，找到对应自己系统的即可。下载后解压到某个目录下，设置一下环境变量即可。这里牵扯到 hugo 和 hugo_extended 两个版本的区别，以下是某个  issue 中的解释：

> I agree.
>
> - The only functional difference is SASS/SCSS
> - The technical build time difference is that it requires a C++ build chain for the target platform to build, the reason why we currently only build the extended for 3 platforms (Windows, Linux, MacOS)
> - Binaries are slightly less portable as you need a compatible Libc version on your computer (for Windows we build a fully static version as Libc is rather uncommon unless you have Visual Studio or something installed).
>
> via: [Please document the difference between the "extended" and non-"extended" versions](https://github.com/gohugoio/hugoDocs/issues/1152)

我认为，直接使用 extended 版本就完事了。使用了 extended 不会报错，而不用 extended 版本可能会报错。

### 构建网站

首先在合适的目录下生成一个新的网站

```shell
$ hugo new site zsblog
```

然后进入目录后，初始化一个 git 仓库

```shell
$ git init
```

[可选]将自己心仪的目录设置为 git 的 [submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) ，需要注意的是，hugo 中的主题配置多是基于 submodule 的，这种方式很灵活，也便于更新，当然，需要先学习一下 submodule 的用法:) ，这里以 coder 主题为例：

```shell
$ git submodule add https://github.com/luizdepra/hugo-coder.git themes/hugo-coder
```

然后基于对应主题的 exampleSite 来设定对应的配置文件 config.toml，**这里很蛋疼的是 coder 主题居然偏爱 toml 配置格式，为什么不是 yaml/json 呢？**

> 补充：hugo 支持三种格式的配置文件 yaml, toml, json. 

然后启动博客即可

```shell
$ hugo server
```

这里很有意思的是，启动多个 server 不会冲突，hugo 会选择另外一个端口部署。同时，启动 server 不会显式生成静态文件。

最后就是上传到 Github，然后可以选用 Github Pages 进行部署，**注意 Baseurl 的设定**。

### 利用 Github Actions 进行持续部署

我这里采用的是将我的博客部署到我的服务器，使用 Github Actions 进行 CI/CD。

使用了 [actions/checkout@v2](https://github.com/actions/checkout)、[peaceiris/actions-hugo@v2](https://github.com/peaceiris/actions-hugo)、[peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)、[burnett01/rsync-deployments@5.1](https://github.com/Burnett01/rsync-deployments) 这几个模板，向大佬表示感谢！

其中有一个很坑的地方是之前用的 rsync 模板是 [contention/rsync-deployments](https://github.com/contention/rsync-deployments)，不知道是为啥···我用这个的时候，只要使用 `--exclude` 参数他就犯病，显示成功但是却没有上传到云端服务器，搁那里 debug 了半天发现是插件好像有点 bug ？有点麻了..

具体的配置文件如下：

```yaml
name: Zs-Hugo-Blog

on:
  push:
    branches:
      - master  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: ${{ github.ref == 'refs/heads/master' }}
        with:
          github_token: ${{ secrets.HUGO_DEPLOY }}
          publish_dir: ./public

      - uses: actions/checkout@v2
        with:
          ref: 'gh-pages'
          
      - name: rsync deployments
        uses: burnett01/rsync-deployments@5.1
        with:
          switches: -avzr --delete --exclude=".htaccess" --exclude="/usr/"
          path: ./
          remote_path: /www/wwwroot/new_blog/
          remote_host: zzsqwq.cn
          remote_user: www
          remote_key: ${{ secrets.SSH_KEY }}
```

### 一些其他配置

再者我还配置了 [Google analytics](https://analytics.google.com/analytics/web/#/)，hugo 对 Google analytics 的支持很不错，点击 [这里](https://gohugo.io/templates/internal/#configure-google-analytics) 查看详情。

此外还有配置了基于 Utterances 的评论系统，起初想要尝试使用 Commento 的，自己搭建了个服务，搞了半天也没搞好，无法在我的网站正常加载，最后还是采用了 Utterances，几分钟就搞好了，可恶。

同时，为了保持原先博客的链接活性，我将原博客部署到了 https://lastblog.zzsqwq.cn ，在 Apache 中将原博客链接全部重定向到当前网站，这样就不会产生死链了，配置如下：

```shell
#rewrite
RewriteEngine On
RewriteRule ^/?index.php/(.*)$ https://lastblog.zzsqwq.cn/index.php/$1 [R,L]
```

## 后记

其中还有一些 RSS 的配置、评论系统宽度的修改等一些小问题，就不细说了。

有个比较蛋疼的问题就是目前 Utterances 评论系统的配色不能随着博客亮暗的切换改变，后续计划改善一下

同时，为了保持主题的可配置性，我 fork 了一份主题，并且针对做了一些修改，仓库地址：[zzsqwq/hugo-coder](https://github.com/zzsqwq/hugo-coder)

