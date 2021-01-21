---
title: Hexo配置
tags: [博客搭建]
categories:
- 博客搭建
- 配置
date: 2019-09-04 10:26:42
entitle: Blog-Hexo-Configuration
---

总结下Hexo**迁移**到新的电脑时需要的配置流程。

<!--more-->

hexo 依赖`git`和`nodejs`确保使用前两者已经安装。

## Hexo安装

```
npm install -g hexo-cli
```

## 主题

我的主题是[melody](https://molunerfinn.com/hexo-theme-melody-doc/zh-Hans/)。

安装
在hexo根目录下执行以下代码。

```
git clone -b master https://github.com/Molunerfinn/hexo-theme-melody themes/melody

```

melody支持平滑升级，配置文件位于hexo根目录下的`source/_data/melody.yml`，迁移和升级的时候都十分方便。

## 搜索

搜索使用了[hexo-algolia](https://github.com/oncletom/hexo-algolia)。

Algolia使用步骤：
首先[注册账户](https://www.algolia.com/)，注册完成后创建新的index。

执行安装命令。
```
npm install hexo-algolia --save
```

在`\_config.yml`中配置algolia。

```
algolia:
  applicationID: 'Application ID'
  apiKey: 'Search-Only API key'
  indexName: 'indexName'
```

修改`Search-Only key`属性。
![algolia](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/hexo/agolia_ACL.png?x-oss-process=style/sw-white)


## 评论

评论使用了[gitalk](https://github.com/gitalk/gitalk)，参照readme进行配置即可。


## ssh key

生成ssh key
```
ssh-keygen -t rsa -C "GitHub注册邮箱"
```

在[github_ssh](https://github.com/settings/keys)新增一个sshkey，将`id_rsa.pub`中的内容复制到key中。

在`\_config.yml`中配置deploy。例子：
```
deploy:
  type: git
  repo: git@github.com:Lee875083146/lee875083146.github.io.git
  branch: master
```
