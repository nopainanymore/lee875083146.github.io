---
title: Markdown进阶语法学习——时序图
entitle: Markdown-Sequence
tags: [博客搭建,Markdown]
categories:
  - 博客搭建
  - Markdown 
date: 2019-04-03 22:32:11
---
介绍如何使用Markdown绘制时序图。
<!--more-->

## 时序图
### 什么是时序图
>时序图(Sequence Diagram)，又名序列图、循序图，是一种UML交互图。它通过描述对象之间发送消息的时间顺序显示多个对象之间的动态协作。它可以表示用例的行为顺序，当执行一个用例行为时，其中的每条消息对应一个类操作或状态机中引起转换的触发事件。


### 时序图的组成部分
* 对象(Object)，对象代表时序图中的对象在交互中所扮演的角色，位于时序图顶部和对象代表类角色。
* 生命线(Lifeline)，生命线代表时序图中的对象在一段时期内的存在。时序图中每个对象和底部中心都有一条垂直的虚线，这就是对象的生命线，对象间 的消息存在于两条虚线间。
* 控制焦点(Activation)，控制焦点代表时序图中的对象执行一项操作的时期，在时序图中每条生命线上的窄的矩形代表活动期。
* 消息(Message)，消息是定义交互和协作中交换信息的类，用于对实体间的通信内容建模，信息用于在实体间传递信息。允许实体请求其他的服务，类角色通过发送和接受信息进行通信。

## Markdown时序图语法
### 标题
title： this is title
### 参与者
participant A
### 消息
participant A
participant B
Note left of A: left Note 
Note Over A: over Note
Note right of A: right Note
Note Over A,B: Note over both A and B
### 动作
participant A
participant B
A->B: Normal line
A-->B: Dashed line
A->>B: Open arrow
A-->>B: Dashed Open arrow

## 例子

渲染问题将·替换为`
···sequence
title: Sequence Demo
participant A as a
participant B as b
Note left of a: left Note
a->b: Normal line
b-->a: Dashed line
a->>b: Open arrow
b-->>a: Dashed Open arrow
···


```sequence
title: Sequence Demo
participant A as a
participant B as b
Note left of a: left Note
a->b: Normal line
b-->a: Dashed line
a->>b: Open arrow
b-->>a: Dashed Open arrow
```

## Hexo支持时序图
### 安装插件
```
npm install --save hexo-filter-sequence
```
### 增加配置
在`_config.yml`中增加以下配置：
```
sequence:
  webfont: https://cdn.bootcss.com/webfont/1.6.28/webfontloader.js
  raphael: https://cdn.bootcss.com/raphael/2.2.7/raphael.min.js
  underscore: https://cdn.bootcss.com/underscore.js/1.8.3/underscore-min.js
  sequence: https://cdn.bootcss.com/js-sequence-diagrams/1.0.6/sequence-diagram-min.js
  css: # optional, the url for css, such as hand drawn theme 
  options: 
    theme: simple
    css_class:
```

### 修改JS文件
进入根路径下的`node_modules/hexo-filter-sequence`。
####  修改`index.js`
将`index.js`，替换为如下代码。
```
var assign = require('deep-assign');
var renderer = require('./lib/renderer');

hexo.config.flowchart = assign({
  webfont: 'https://cdnjs.cloudflare.com/ajax/libs/webfont/1.6.27/webfontloader.js',
  raphael: 'https://cdn.bootcss.com/raphael/2.2.7/raphael.min.js',
  underscore: 'https://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.8.3/underscore-min.js',
  sequence: 'https://cdnjs.cloudflare.com/ajax/libs/js-sequence-diagrams/1.0.6/sequence-diagram-min.js',
  css: '',
  options: {
    theme: 'simple'
  }
}, hexo.config.flowchart);

hexo.extend.filter.register('before_post_render', renderer.render, 9);
```

####  修改`/lib/renderer.js`
将28-31行替换为以下内容：
```
      data.content += '<script src="' + config.webfont + '"></script>';
      data.content += '<script src="' + config.raphael + '"></script>';
      data.content += '<script src="' + config.underscore + '"></script>';
      data.content += '<script src="' + config.sequence + '"></script>';
```
