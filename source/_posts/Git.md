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

* -a 将未执行`add`命令的修改一起提交
* -m '提交信息'，用来输入提交的信息

## branch

Git的分支也很轻量，它们只是简单地指向某个提交记录，Git创建再多的分支也不会造成存储或内存上的开销，并且按照逻辑分解工作到不同的分支比维护臃肿的分支更加的简单。

分支的含义即为：基于这个提交以及它所有的父提交进行新的工作。

* 查看本地分支
* <分支名> 创建新的分支
* -r 查看远程分支
* -a 查看所有分支
* -f <分支名> <提交记录> 强制修改分支位置到指定到提交记录

## checkout

* <分支名> 检出目标分支
* <记录标签哈希值> 分离`HEAD`
* -b 创建新的分支并检出

分支名或者记录标签哈希值配合 `^` 和 `~<num>`起到相对引用的作用：
* 使用 `^`向上移动 1 个提交记录
* 使用 `~<num>` 向上移动多个提交记录，如 ~3

## merge

`merge`将两个分支合并到一起，即把两个父节点本身及它们所有的祖先都包含进来。

`git merge <分支名>`

## rebase

`rebase`就是取出一系列的提交记录，复制它们，然后在另外一个地方逐个放下去。

`rebase`可以创造更线性的提交历史，使提交树变得很干净, 所有的提交都在一条线上，但修改了提交树的历史。

`git rebase destination source`

* -i <操作的最远提交记录的父记录> ，交互式`rebase`，Git会打开一个UI界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。UI界面下可以做三件事
 * 调整提交记录的顺序
 * 删除不想要的提交
 * 合并提交

## reset

`reset`通过分支记录回退几个提交记录来实现撤销改动。

`git reset <回退到的目标记录>`

## revert

`revert`撤销更改并分享给别人。要撤销的提交记录后面多了一个新提交，新提交记录引入了更改，这些更改刚好是用来撤销这个提交的。

`revert`之后就可以把更改推送到远程仓库。

`git revert <要回滚的记录>`

## cherry-pick

`cherry-pick`将一些提交复制到当前所在的位置`（HEAD）`下面。

`git cherry-pick <提交号>...`

## tag

tag可以永久地将某个特定的提交命名为里程碑，然后就可以像分支一样引用了。它们并不会随着新的提交而移动。你也不能检出到某个标签上面进行修改提交，它就像是提交树上的一个锚点，标识了某个特定的位置。

`git tag <tag名> <记录>`

## describe

describe用来描述离你最近的标签，能帮你在提交历史中移动了多次以后找到方向。

`git describe <ref>`

``<ref>``可以是任何能被`Git`识别成提交记录的引用，如果没有指定的话`Git`会以目前所检出的位置``（HEAD）``。

它输出的结果是这样的：

`<tag>_<numCommits>_g<hash>`

`tag`表示的是离`ref`最近的标签，`numCommits`是表示这个`ref`与`tag`相差有多少个提交记录，`hash`表示的是所给定的`ref`所表示的提交记录哈希值的前几位。

当`ref`提交记录上有某个标签时，则只输出标签名称。

## clone

远程仓库：
* 是本地仓库在另一台计算机上的拷贝，可以通过增加获取提交记录与远程仓库进行通信
* 远程仓库是一个强大的备份，本地仓库有恢复文件到指定版本的能力，但所有信息都是保存在本地的，远程仓库保证在本地仓库丢失数据的情况下从远程仓库获取
* 远程仓库使代码社交化

本地仓库中带有```o/```或者```origin/```的分支即为远程分支。远程分支反映了远程仓库（在你上次和他通信时）的状态，在检出远程分支时，将会自动进入分离`HEAD`状态，Git限制不能直接在这些分支上操作，必须完成你的工作，更新远程仓库之后再分享工作成果。

远程分支的命名规范如下：`<remote name>/<branch name>`，Git默认会将远程仓库名设置为`origin`。


## fetch

`git fetch`完成了仅有的但是很重要的两步:
* 从远程仓库下载本地仓库中缺失的提交记录
* 更新远程分支指针(如 `o/master`)

`git fetch`实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。

`git fetch`通常通过互联网（使用 http:// 或 git:// 协议) 与远程仓库通信。

`git fetch` 并不会改变本地仓库的状态。不会更新`master`分支，也不会修改磁盘上的文件。

`git fetch`的参数和`git push`极其相似。他们的概念是相同的，只是方向相反。

`git fetch`，会下载所有的提交记录到各个远程分支。

如果像如下命令这样为`git fetch`设置`<place>`的话：

`git fetch origin foo`

Git会到远程仓库的`foo`分支上，然后获取所有本地不存在的提交，放到本地的`o/foo`上。

通过指定 `place...`

只下载了远程仓库中 `foo` 分支中的最新提交记录，并更新了 `o/foo`

如果指定`<source>:<destination>`会发生什么？

例如：
`git fetch origin foo~1:bar`

Git将`foo~1`解析成一个`origin`仓库的位置，然后将那些提交记录下载到了本地的`bar`分支（一个本地分支）上。

如果你觉得直接更新本地分支很爽，那你就用冒号分隔的 refspec 吧。不过，你不能在当前检出的分支上干这个事，但是其它分支是可以的。

需要注意的是`source`现在指的是远程仓库中的位置，而`<destination>`才是要放置提交的本地仓库的位置。

`git fetch origin :bugFix`

如果`fetch`**空**`<source>`到本地，会在本地创建一个新分支。


## pull

`git pull`到头来就是`fetch`后跟`merge`的缩写。可以理解为用同样的参数执行`git fetch`，然后再`merge`所抓取到的提交记录。

`git pull`实际上就是`fetch`+`merge`的缩写，`git pull`唯一关注的是提交最终合并到哪里（也就是为`git fetch`所提供的`destination`参数）。

以下命令在 Git 中是等效的:

例如：
`git pull origin foo` 相当于：

`git fetch origin foo; git merge o/foo`

`git pull origin bar~1:bugFix` 相当于：

`git fetch origin bar~1:bugFix; git merge bugFix`

`git pull origin master:foo`在本地创建了一个叫`foo`的分支，从远程仓库中的`master`分支中下载提交记录，并合并到`foo`，然后再`merge`到我们的当前检出的分支`bar`上。

## push

`git push <remote> <place>`

例如：
`git push origin master`

切到本地仓库中的`master`分支，获取所有的提交，再到远程仓库`origin`中找到`master`分支，将远程仓库中没有的提交记录都添加上去。

通过`place`参数来告诉Git提交记录来自于 `master`, 要推送到远程仓库中的` master`。它实际就是要同步的两个仓库的位置。

需要注意的是，因为通过指定参数告诉了 Git 所有它需要的信息, 所以它就忽略了所检出的分支的属性。

`<place>`参数详解:
当为 git push 指定 place 参数为 master 时，我们同时指定了提交记录的来源和去向

如果来源和去向分支的名称不同呢？比如你想把本地的 foo 分支推送到远程仓库中的 bar 分支。

要同时为源和目的地指定 `<place>` 的话，只需要用冒号`:`将二者连起来就可以了：

`git push origin <source>:<destination>`

例如：
`git push origin foo:bar`

如果你要推送到的目的分支不存在,Git会在远程仓库中根据你提供的名称帮你创建这个分支。

`git push origin :side`
`push`**空**`<source>`到远程仓库，它会删除远程仓库中的分支。

## gitignore

1. 清除tracked缓存`git rm -r --cached .`
2. 重新添加文件`git add .`
3. 最后需要提交`git commit -m ".gitignore is now working"`
