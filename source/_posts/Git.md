---
title: Git
tags: [Git]
categories:
- Git
date: 2019-12-06 16:17:22
entitle: Git
---

Git作为最优秀的版本控制工具，无论是在工作还是自己的项目中都非常重要。
本文按照[Git可视化学习网站](https://learngitbranching.js.org/)学习Git常用的命令，并做了整理，推荐大家也去练习练习。

<!--more-->

## commit

Git仓库中的提交记录保存的是目录下所有文件的快照，在每次提交时Git会将当前版本与仓库上个版本进行对比，把两者之间的差异打包作为一个提交记录。同时Git还保存了提交的历史记录。

```git
git commit -a -m '提交信息'
```

* -a 将未执行add命令的修改一起提交
* -m '提交信息'，用来输入提交的信息

## branch

Git的分支也很轻量，它们只是简单地指向某个提交记录，Git创建再多的分支也不会造成存储或内存上的开销，并且按照逻辑分解工作到不同的分支比维护臃肿的分支更加的简单。

分支的含义即为：基于这个提交以及它所有的父提交进行新的工作。


## checkout

## merge

## rebase


`-i`

优点:


Rebase 使你的提交树变得很干净, 所有的提交都在一条线上
缺点:

Rebase 修改了提交树的历史

## reset

## revert

## cherry-pick


## tag

## describe

```git
git describe <ref>
```

``<ref>``可以是任何能被`Git`识别成提交记录的引用，如果你没有指定的话`Git`会以你目前所检出的位置``（HEAD）``。

它输出的结果是这样的：

```
<tag>_<numCommits>_g<hash>
```

`tag`表示的是离`ref`最近的标签，`numCommits`是表示这个`ref`与`tag`相差有多少个提交记录，`hash`表示的是你所给定的`ref`所表示的提交记录哈希值的前几位。

当`ref`提交记录上有某个标签时，则只输出标签名称。

## clone

直接了当地讲，master 和 o/master 的关联关系就是由分支的“remote tracking”属性决定的。master 被设定为跟踪 o/master —— 这意味着为 master 分支指定了推送的目的地以及拉取后合并的目标。

克隆时, Git 会为远程仓库中的每个分支在本地仓库中创建一个远程分支（比如 o/master）。然后再创建一个跟踪远程仓库中活动分支的本地分支，默认情况下这个本地分支会被命名为 master。

克隆完成后，你会得到一个本地分支（如果没有这个本地分支的话，你的目录就是“空白”的），但是可以查看远程仓库中所有的分支（如果你好奇心很强的话）。这样做对于本地仓库和远程仓库来说，都是最佳选择。



## fetch




git fetch 完成了仅有的但是很重要的两步:

* 从远程仓库下载本地仓库中缺失的提交记录
* 更新远程分支指针(如 o/master)


git fetch 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。

git fetch 通常通过互联网（使用 http:// 或 git:// 协议) 与远程仓库通信。

git fetch 的参数和 git push 极其相似。他们的概念是相同的，只是方向相反罢了（因为现在你是下载，而非上传）

如果你像如下命令这样为 git fetch 设置 <place> 的话：

git fetch origin foo

Git 会到远程仓库的 foo 分支上，然后获取所有本地不存在的提交，放到本地的 o/foo 上。

通过指定 place...

我们只下载了远程仓库中 foo 分支中的最新提交记录，并更新了 o/foo


如果我们指定 <source>:<destination> 会发生什么呢？”

如果你觉得直接更新本地分支很爽，那你就用冒号分隔的 refspec 吧。不过，你不能在当前检出的分支上干这个事，但是其它分支是可以的。

这里有一点是需要注意的 —— source 现在指的是远程仓库中的位置，而 <destination> 才是要放置提交的本地仓库的位置。它与 git push 刚好相反，这是可以讲的通的，因为我们在往相反的方向传送数据。

理论上虽然行的通，但开发人员很少这么做。我在这里介绍它主要是为了从概念上说明 fetch 和 push 的相似性，只是方向相反罢了。

git fetch origin :bugFix

如果 fetch 空 <source> 到本地，会在本地创建一个新分支。

很神奇吧！但无论怎么说, 这就是 Git！

## pull
git pull 到头来就是 fetch 后跟 merge 的缩写。你可以理解为用同样的参数执行 git fetch，然后再 merge 你所抓取到的提交记录。

以下命令在 Git 中是等效的:

git pull origin foo 相当于：

git fetch origin foo; git merge o/foo

还有...

git pull origin bar~1:bugFix 相当于：

git fetch origin bar~1:bugFix; git merge bugFix




## push

git push <remote> <place>

git push origin master

把这个命令翻译过来就是：

切到本地仓库中的“master”分支，获取所有的提交，再到远程仓库“origin”中找到“master”分支，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我。

我们通过“place”参数来告诉 Git 提交记录来自于 master, 要推送到远程仓库中的 master。它实际就是要同步的两个仓库的位置。

需要注意的是，因为我们通过指定参数告诉了 Git 所有它需要的信息, 所以它就忽略了我们所检出的分支的属性！


<place>参数详解:当为 git push 指定 place 参数为 master 时，我们同时指定了提交记录的来源和去向


 如果来源和去向分支的名称不同呢？比如你想把本地的 foo 分支推送到远程仓库中的 bar 分支。

 要同时为源和目的地指定 <place> 的话，只需要用冒号 : 将二者连起来就可以了：

git push origin <source>:<destination>

这个参数实际的值是个 refspec，“refspec” 是一个自造的词，意思是 Git 能识别的位置（比如分支 foo 或者 HEAD~1）



git push origin :side
 push 空 <source> 到远程仓库会如何呢？它会删除远程仓库中的分支！
