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

## commit

## branch

## checkout

## merge

## rebase


`-i`

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
