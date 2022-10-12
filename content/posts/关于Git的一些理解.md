---
title: "关于Git的一些理解"
categories: [ "Ubuntu","Git" ]
tags: [ "Git" ]
draft: false
slug: "201"
date: "2021-07-23 14:17:00"
---

# 前言

前段时间在 Github 学完了关于 git 的小游戏 [learnGitBranching](https://github.com/pcottle/learnGitBranching) ，受益匪浅。

它通过可视化的方式将分支的关系，每条命令的作用等都明明白白的体现出来，可以很直观的感受到你每条命令对整个分支树，每一个 ref 的作用。

通过这种学习感觉自己对 Git 的理解更加深入一步，能够理解其中的原理，而不是浅尝辄止，照猫画虎。

学习中记了一些零零散散的思路，想要写一篇笔记记录出来，之前已经写过一个简单的 Git 教程，这篇教程将会更加深入，希望可以帮助大家更好的掌握 Git。

窃认为，想要学好 Git ，必须要理解清楚其中的分区以及引用，学会了这两个，各种基本操作就很容易理解了。接下来的笔记也基本以此思路展开。



# Git中的分区

![Git中的三大分区，图片来源自掘金GabrielPanda][1]

首先，Git中存在三大分区，分别是**工作区、暂存区、版本库**。其中，

**工作区**即我们工作的目录，**暂存区**是我们执行 `git add` 后文件存在的区域。

我们可以通过 `git status` 对两种状态进行查看，例如：

```sheel
~/test master*
base ❯ git status  
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   test

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   test
```

上图中存在两部分， 分别为 `Changes to be committed` 这里是表示的版本库与暂存区的区别，还有`Changes not staged for commit` ，它表示的是工作区与暂存区的区别。

**版本库**是我们执行 `git commit -m "xxx"` 后，文件存在的区域。在上述过程中，Git 记录暂存区与版本库的差异，生成版本号，记录下来。我们可以通过 `git log` 来查看我们产生的更改，内容如下：

```shell
commit 9da52a0e4800547ca46bd6bb919d1105cea43f1e (HEAD -> master)
Author: zs <2459958352@qq.com>
Date:   Thu Jul 22 22:09:45 2021 +0800

    test commit
```

其中包含了版本号、当前节点上的 ref 记录、作者、邮箱、日期以及此次提交的注释。



# Git中的引用

在 Git 中，引用到处可见，引用类似于给某一个 `commit-id` 即某一次提交的 `SHA-1` 值起一个简单的名字，如 `branch` ，`tag` 这些都是引用。

Git 中存在一个命令， `git update-ref` ，你可能几乎没见过，但可能天天在用。

当运行类似于 `git branch <branch>` 这样的命令时，Git 实际上会运行 `git update-ref` 命令，例如，运行 `git branch zs`，就等效于

```shell
$ git update-ref refs/heads/zs <commit-id>
```

这里的 `commit-id` 就是当前提交的 `commit-id` ，那他是如何获得的呢？ 

使用过 Git 的人一定知道，Git 中存在一个名叫 `HEAD` 的引用，它可能是引用，也可能是引用的引用。即它很多时候是指向某一个引用，如指向分支 `master` 这个引用。不过，它也可以与引用分离，称为`游离的HEAD`，即不指向某个引用，而指向单独的一个 `commit` 。

可以通过 `git commit commit-id` 来实现，不过，我们一般**不推荐这种操作**。下面的讨论，我们都是基于 `HEAD` 是指向某一分支的。

下面是关于一个引用的小例子：

```shell
~/test master*
base ❯ git branch zs                                                    

~/test master*
base ❯ cat .git/refs/heads/zs 
9da52a0e4800547ca46bd6bb919d1105cea43f1e

~/test master*
base ❯ git update-ref refs/heads/test 9da52a

~/test master*
base ❯ cat .git/HEAD             
ref: refs/heads/master

~/test master*
base ❯ cat .git/refs/heads/master
9da52a0e4800547ca46bd6bb919d1105cea43f1e

~/test master*
base ❯ cat .git/refs/heads/test 
9da52a0e4800547ca46bd6bb919d1105cea43f1e
```

可以发现，例子中 HEAD 指向 master ，master,zs,test 同时都指向 id 为 `9da52a` 的提交。



# 撤销更改

## 撤销本地更改

+ 通过**版本库**撤销**暂存区**更改，**工作区**不改

```shell
$ git reset HEAD^ #撤销一次更改
$ git reset HEAD~nums #撤销HEAD往前nums次更改
```

+ 直接通过**版本库**撤销**工作区**的更改

```shell
$ git reset HEAD^ --hard     #撤销一次更改
$ git reset HEAD~nums --hard #撤销HEAD往前nums次更改
```

可以发现只要加了 `--hard` ，就可以直接也把工作区改掉，不过建议三思而后行！

## 撤销远程更改

上面说的是你本地的**工作区** or **暂存区**的撤销

如果你已经把更改推送到了远端仓库，那么你想要去掉那次改动怎么办？

可能你会想，直接通过 `git reset` 切换到上面的某个需要的节点，然后再改？But，很容易想到这样会产生严重的冲突。**一旦commit已经被push到远程仓库，那么是坚决不允许去reset它的。**

还好，Git 给我们提供了一个更好的选择，你可以通过 `git revert` 产生一个类似于补丁的东西来消除掉更改，很容易理解，这样没有改变树的结构，相对于 `git reset` 他会往前走而不是回溯，这不会对之前的历史产生重要的影响。 

需要注意的是， `git revert ` 的用法：

```shell
$ git revert HEAD #撤销掉HEAD这次更改，回到HEAD的上次版本
$ git revert <commit_id> #撤销掉这次cmmit的修改
```





# 两种合并方式

我们知道，Git 中存在两种合并分支的方式，分别为 `git merge` 和 `git rebase` 。

两种方式各有优劣，简单说，`rabase` 是把两条分支的提交记录整理到某一主分支上，它有着历史的完整记录。而 `merge` 虽然也是整理了提交，但是某一分支的中间提交更改的过程合并后并不会体现在主分支上，中间过程可以说是在主分支上不可见的。

很容易发现，`rebase` 产生的主分支提交记录会更加的详细，它记录了每一步小的改动。而 `merge` 产生的更简洁，有点类似于封装的意味，只是告诉你我这个提交完成了这个任务的开发，内部的实现细节却不会告诉你。 

而 `rebase` 相比与 `merge` 也会更加繁琐一些，你也可以通过 `git rebase -i` 来通过可视化界面（可视化文本列表）的方式，来对记录做取舍与改动，不过还是没有 `merge`  方便，远程仓库的合并操作一般都是使用  `merge` 。

需要注意的是，两种方法的使用习惯很不一样：

```shell
$ git merge <branch>
```

代表的是将 `<branch>` 分支合并到当前 `HEAD` 所在的分支。

```shell
$ git rebase <branch1> <branch2>
```

代表的是将 `branch2` 合并到 `<branch1>` 的位置。如果省略 `<branch2>` ，那么就是合并 `HEAD` 所在分支到 `<branch1>` 分支。

可以发现 `merge` 体现的是一种， `merge xx` 到当前位置。而 `rebase` 体现的是将自己合并到 `xx` 那里去。一个是别人过来，一个是自己过去。建议两个命令改成 `merge from` ，`rebase into`，哈哈。

顺便提一下， `merge` 会导致一个节点有多个父节点，通过上文我们知道可以通过 `~` 在一条线上移动，在这里，我们可以通过 `^` 来指定第几个父节点，如 `HEAD^3` 就是指 `HEAD` 所在节点的第三个父节点。



# 整理提交记录

我们有时会需要把另一个分支的部分更改放到主分支上来，即整理我们所有的提交记录，拿到我们所需要的来组成一个完整功能。

为了完成这件事，我们想到，这有点像合并分支，不过可能不需要某一个分支上的全部更改，只需要其中的一部分就可以了。

上面提到，通过 `git rebase -i` 可以进行交互式的 `rebase` ，可以对提交记录进行取舍，因此这样就可以满足我们的需求，只不过可能合并的时候需要想明白是从哪里变到哪里，有一些烧脑。

幸运的是，Git 还给我们提供了另一个更加简洁的方式——`git cherry-pick` ，语法如下：

```shell
$ git cherry-pick <commid-id>
```

你可以通过这种方式，将树上的任意一个节点的提交添加到当前 `HEAD` 所在分支的下方，这真是功能强大的命令！你也可以通过空格间隔，来顺序摘取多个提交。



# 两条万能指令

除了上面我觉得值得说道的问题，我还想推荐两条我认为非常有用的指令，掌握了他们，你就可以在分支树上随心所欲的移动！

+ 移动分支

```shell
$ git branch -f <branch> <commit-id>
```

通过这条指令，你可以将 `<branch>` 的引用指向 `commit-id` ，如果你读懂了上面的内容，你会发现它只是通过 `update-ref` 更新了对应的引用。

+ 移动 HEAD

```shell
$ git checkout <branch>
$ git checkout <commit-id>
```

通过这条指令，你可以自由的移动 `HEAD` 引用，前者让他指向了 `<branch>` 分支，后者让他指向了SHA-1为 `<commit-id>` 的提交。



# 推荐的资料

在学习的过程中，看了很多资料，一并推荐给大家！

+ [learnGitBranching](https://github.com/pcottle/learnGitBranching)：有趣的闯关游戏，但也干货满满。
+ [Git三大分区概念](http://events.jianshu.io/p/9b3d7b6fe738)：讲解了关于分区的概念，还有直观清晰的图片！
+ [Git的引用](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%BC%95%E7%94%A8)：来自 Git 官方的讲解，十分硬核。


  [1]: https://blog.zzsqwq.cn/usr/uploads/2021/07/612956951.jpg