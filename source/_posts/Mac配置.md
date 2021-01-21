---
title: Mac配置
tags: [Mac]
categories:
- Mac
- 配置
date: 2019-09-04 10:28:53
entitle: Mac-Configuration
---

Mac终端配置和软件推荐。

<!--more-->

brew 和oh my zsh 安装或许需要设置代理。

## brew

Mac 包管理工具。

安装方式：
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

作用：
安装任意包。

```bash
安装任意包
$ brew install <packageName>

卸载任意包
$ brew uninstall <packageName>

查询可用包
$ brew search <packageName>

查看已安装包列表
$ brew list

查看任意包信息
$ brew info <packageName>

更新Homebrew
$ brew update

查看Homebrew版本
$ brew -v

Homebrew帮助信息
$ brew -h

安装macOS应用
$ brew cask install <appName>
```

## oh my zsh

Mac 最强大的终端

```bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

我的zsh配置：
```
# zsh主题
ZSH_THEME="ys"

# zsh插件
plugins=(git autojump)

[ -f /usr/local/etc/profile.d/autojump.sh ] && . /usr/local/etc/profile.d/autojump.sh
```

`autojump`，跳转工具，`autojump`会记录`cd`的目录历史，使用`j <targetDir>`可以快速跳转的目标路径， targetDir 可以是全路径也可以是路径的某个词。

```bash
$ brew install autojump
```

## Markdown编辑器

`Markdown`的编辑器有很多，个人倾向于`Typora`，习惯`Markdown`语法的可以直接在`source mode`下以原声语法的形式写，不习惯的可以可以使用`typora`丰富的快捷键。

`typora`支持`markdown`内联公式及各种图表。

原生支持导出PDF、HTML，结合[pandoc](https://github.com/jgm/pandoc)可以导出Word、Epub等格式。

`Atom`是Github开发的文本编辑器，有丰富的插件，也挺不错，最近在适用。

## 视频播放器

视频播放器使用的是`Movist Pro`。


## 下载器

Bt下载器使用的是qBittorrent，结合[tracker](https://github.com/ngosang/trackerslist)，常见的资源可以有不错的下载速度。
