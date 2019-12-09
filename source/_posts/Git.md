---
title: Git
tags: [Git]
categories:
- Git
date: 2019-12-06 16:17:22
entitle: Git
---

总结Git常用命令。

推荐Git可视化学习网站<https://learngitbranching.js.org/>。

<!--more-->


直接了当地讲，master 和 o/master 的关联关系就是由分支的“remote tracking”属性决定的。master 被设定为跟踪 o/master —— 这意味着为 master 分支指定了推送的目的地以及拉取后合并的目标。

克隆时, Git 会为远程仓库中的每个分支在本地仓库中创建一个远程分支（比如 o/master）。然后再创建一个跟踪远程仓库中活动分支的本地分支，默认情况下这个本地分支会被命名为 master。

克隆完成后，你会得到一个本地分支（如果没有这个本地分支的话，你的目录就是“空白”的），但是可以查看远程仓库中所有的分支（如果你好奇心很强的话）。这样做对于本地仓库和远程仓库来说，都是最佳选择。



## commit

## branch

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


## fetch

git fetch 完成了仅有的但是很重要的两步:

* 从远程仓库下载本地仓库中缺失的提交记录
* 更新远程分支指针(如 o/master)


git fetch 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。

git fetch 通常通过互联网（使用 http:// 或 git:// 协议) 与远程仓库通信。


## pull



## push

git push <remote> <place>

git push origin master

把这个命令翻译过来就是：

切到本地仓库中的“master”分支，获取所有的提交，再到远程仓库“origin”中找到“master”分支，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我。

我们通过“place”参数来告诉 Git 提交记录来自于 master, 要推送到远程仓库中的 master。它实际就是要同步的两个仓库的位置。

需要注意的是，因为我们通过指定参数告诉了 Git 所有它需要的信息, 所以它就忽略了我们所检出的分支的属性！
