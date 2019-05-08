---
title: Markdown进阶语法学习——流程图
entitle: Markdowm-Flow
date: 2019-03-23 22:55:17
tags: [博客搭建,Markdown]
categories:
    - 博客搭建
    - Markdown
---
介绍如何使用Markdown绘制流程图。
<!--more-->
## 注意
Hexo原生是不支持Markdown的流程图语法的。
执行以下命令以支持流程图。
```
npm install --save hexo-filter-flowchart
```


## 步骤及语法

### 步骤
1. 定义流程图的元素
2. 连接流程图元素

### 语法
1. 定义阶段语法
`tag=>type: content :>url`
* tag：流程图中的标签，即元素的名称，在连接阶段指定连接顺序
* type：标签类型，共有六种

标签|含义
:-:|:-:
start| 开始
end| 结束
operation| 处理块
subroutine| 子程序块
condition| 条件判断
inputoutput|输入输出

* content：流程图文本框中的描述内容
* url：与文本框中文字绑定的链接
2. 连接阶段语法
`tag1->tag2->tag3`
* 使用`->`连接元素
* 对于`condition`类型，有`yes`，`no`两个分支，用法： `condTag(yes)`，`condTag(no)`
* 每个元素可以指定分支的走向，默认向下，使用`left`，`right`来指定左右，用法：`tag(left)`

## 例子

###  生成后的流程图
```flow
start=>start: 开始
input1=>inputoutput: 请输入密码
op1=>operation: 对密码加密
con1=>condition: 密码是否正确
op2=>operation: 登录成功
end=>end: 结束
start->input1->op1->con1
con1(yes)->op2->end
con1(no)->end
```

### 流程图Markdown代码


	···flow(由于渲染问题请将·替换为\`)
	start=>start: 开始
	input1=>inputoutput: 请输入密码
	op1=>operation: 对密码加密
	on1=>condition: 密码是否正确
	op2=>operation: 登录成功
	end=>end: 结束
	start->input1->op1->con1
	con1(yes)->op2->end
	con1(no)->end
	···
