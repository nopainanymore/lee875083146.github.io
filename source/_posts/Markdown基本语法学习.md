---
title: Markdown基本语法学习
entitle: Markdown-Grammer
date: 2019-03-03 10:47:05
tags: [博客搭建,Markdown]
categories:
    - 博客搭建
    - Markdown
---

最近开始写博客，记录学习的过程，博客使用[Hexo](https://hexo.io/zh-cn/)搭建，下一篇会介绍结合GitHub搭建的过程。

<!--more-->

## Markdown介绍

[Markdown](https://zh.wikipedia.org/wiki/Markdown)是一种纯文本的标记语言,创始人为John Gruber。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML（或者HTML）文档”。
推荐使用[Typora](https://typora.io/  "简洁强大的Markdown编辑器")，编辑Markdown，支持快捷键、及Markdown原生语言两种编写方式。

## Markdown语法

### 标题

Markdown的标题使用`#`声明，格式为 `# 空格 标题名称`，`#`的数量即为标题的等级，最多支持六级标题。

示例：
```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```
> # 一级标题
> ## 二级标题
> ### 三级标题
> #### 四级标题
> ##### 五级标题
> ###### 六级标题

### 字体

```
斜体： *斜体*
加粗： **加粗**
加粗斜体： ***加粗斜体***
删除线： ~~删除线~~
```

*斜体*
**加粗**
***加粗斜体***
~~删除线~~


### 引用

在引用的文字前加>即可。

```
	> 引用
```

> 引用


### 链接

Markdown的链接方式有两种，行内式和参考式。

#### 行内式

```
[Lee's Blog](https://lee875083146.github.io/HexoBlog/ )
[Lee's Blog](https://lee875083146.github.io/HexoBlog/ "Lee's Blog")

[]中写链接的文字，()中为链接地址，()中""可以为链接指定title，链接地址与title中有一个空格。
```

[Lee's Blog](https://lee875083146.github.io/HexoBlog/ )
[Lee's Blog](https://lee875083146.github.io/HexoBlog/ "Lee's Blog")

#### 参考式

```
[Lee's Blog][1]，[Google][2]，[GitHub][]
[1]:https://lee875083146.github.io/HexoBlog/
[2]:https://www.google.com/
[Github]:https://github.com/
参考式分为两个部分，第一个[]中是链接的文字，第二个[]为链接标记，适用于学术论文，或者同一链接在文中有多个地方是引用，方便对链接统一管理。
```

[Lee's Blog][1]，[Google][2]，[GitHub][]
[1]:https://lee875083146.github.io/HexoBlog/  "Lee's Blog"
[2]:https://www.google.com/
[Github]:https://github.com/

#### 自动链接


```
<https://www.google.com/>
```
<https://www.google.com/>

### 上标

使用`[^1]`生成上标。上标[^1]。

### 代码引用

```
单行代码使用`将代码包起来即可
`Hello Markdown`
```

`Hello Markdown`

```
多行代码使用```置于代码段的首行和末行
```

```
Hello
Markdown
```

### 分割线

三个或者三个以上的 - 或者 *



### 列表

#### 无序列表

```
使用 *，+，-表示无序列表
* 1
* 2
* 3
```
* 1
* 2
* 3

#### 有序列表

```
有序列表使用数字接英文据点表示
1. 一
2. 二
3. 三
```
1. 一
2. 二
3. 三

#### 列表嵌套

```
上一级和下一级之间敲三个空格即可
* 一级
   *二级1
   *二级2
```
* 一级
   * 二级1
   * 二级2


### 表格

```
第一行为表头，第二行分隔表头和主体部分，第三行开始每一行为一个表格行。 列于列之间用管道符|隔开。原生方式的表格每一行的两边也要有管道符。
文字默认居左,-两边加：表示文字居中,-右边加：表示文字居右
表头|表头|表头
-|-|-
内容|内容|内容
内容|内容|内容

表头|表头|表头
:-:|:-:|:-:
内容|内容|内容
内容|内容|内容
```
表头|表头|表头
-|-|-
内容|内容|内容
内容|内容|内容

表头|表头|表头
:-:|:-:|:-:
内容|内容|内容
内容|内容|内容

********************************************************************

### 锚点

```
锚点其实就是页内超链接，也就是链接本文档内部的某些元素，实现当前页面中的跳转。
在标题后插入锚点{#锚点标记}
锚点[锚点](#1)
```

### 图片 

```
![图片名称](图片地址 ''title'')
图片名称显示在图片下面的文字，相当于对图片内容的解释。
title是图片的标题，当鼠标移到图片上时显示的内容。title可加可不加
![我的头像](http://ww1.sinaimg.cn/large/005yN1Nlgy1g0lee3rjxbj311s0scgpu.jpg "我的头像")
```

![我的头像](https://nopainanymore.oss-cn-hangzhou.aliyuncs.com/favicon.jpg?x-oss-process=style/sw-white "我的头像")

[^1]:上标举例
