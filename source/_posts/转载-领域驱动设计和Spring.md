---
title: '[转载]领域驱动设计和Spring'
entitle: DDD-Spring
tags: [领域驱动设计]
categories:
  - 领域驱动设计
date: 2019-04-18 23:44:46
---


本文转载自 JoeCao <http://github.com/JoeCao/JoeCao.github.io/issues/8>
<!--more-->
## 介绍
这篇文章是的介绍一下领域驱动设计的基础构件、概念和Java的web应用（主要是基于Spring框架）之间的关系和区别。
这篇文章的第二部分讲了怎么把实体、聚合根、仓储映射到使用Spring框架的Java应用中
## 领域驱动设计
Eric Evans的《领域驱动设计》无疑是软件设计领域最重要的几本书之一。
这本书主要集中在软件开发中如何处理领域和软件的映射关系— 开始强调领域通用语言(domain ubiquitous language)，通过语言来提取模型，最终映射到一个可工作的软件上。
我们已经对软件设计模式比较熟悉了，他是用于描述和提炼Class和Class关系的技术语言。而DDD是一种用于程序员和业务沟通的更通用的语言，使用DDD可以最终将代码映射到模型上。

### 基础构件
构件是DDD中的一些专有名词，让我们看一下图
![DDD的专有名词](https://ws1.sinaimg.cn/large/0078YTE8gy1g2782r4t8jj31kw154q9b.jpg)

#### 限界上下文（Bounded Context)
当进行领域建模的时候，任何将其作为一个整体进行建模的尝试注定会失败。因为各类利益相关者和他们对领域的看法可能完全不同，试图建立一个单一的、独特的模型来满足所有需求是完全不可能的，会把系统搞得极为复杂。
让我们看一个示例图，这个图描述了销售领域已经识别出的模型
![销售领域](https://ws1.sinaimg.cn/large/0078YTE8gy1g2xqfc5cwoj31jn105tff.jpg)

我们把模型元素稍加区分，成为分离的模型，就可以看出客户和订单的，他们是不同上下文的核心的概念。
![区分](https://ws1.sinaimg.cn/large/0078YTE8gy1g278398kp7j31kw0yjjwd.jpg)

在这里，我们确定了系统战略层面的核心部分，这些部分可能都涉及客户或订单的概念，但通常不同限界上下文对它们的属性感兴趣的部分并不相同。比如 Accounting上下文通常对客户的计费信息和不同的支付选项感兴趣，而Shipping上下文的对客户的唯一目标是运送地址，然后跟踪订单。 Order上下文可能通过客户的订单项了解商品信息，但实际上只涉及商品类目基本的内容（译者注：商品规格、商品详情这些信息Order上下文并不关注）。
![概念在不同的上下文中](https://ws1.sinaimg.cn/large/0078YTE8gy1g27831iv0uj31kw0yctfq.jpg)

这里的模型元素可能看起来会以不同的方式反映在系统的不同层面中（译者：指的是Product和Customer对象会存在多个领域中，但是不同的概念）。现代的软件架构甚至更进一步，积极的通过冗余和最终一致性降低单个系统的复杂度，提升系统弹性，并提高整体的开发生产力（译者：CQRS和事件驱动架构）

#### 值对象
代表了领域对象中的概念 — 值 （和他的名字一样），值对象没有主键没有生命周期。而且他是不可变的，以确保可以在多个消费者之间完整的共享。在Java中，String对象是符合这些特性的一个很好的例子，不过这不太合适领域设计的例子，因为他太抽象了。一般来说，信用卡号码、电子邮箱地都可以很好的被构建为值对象。值对象常常是实体的属性。将领域元素实现为值对象可以大大增强代码的可读性，正如Dan Bergh Johnsson在他的DDD中的Power Use of Value Objects所展示的。
由于需要实现Getter、equals()、hashCode()方法，纯Java实现值对象相当麻烦（译者：特别是没有Builder方法）。所以很多人不愿意做，值对象往往被遗忘。现在你可以用Lombok这个工具来方便的构造处理值对象。

#### 实体类
和值对象相比，实体的核心特征就是他是有主键。两个都叫Michael Muller的客户可能不是一个实例，我们通常会引入一个专用的属性来识别他们。另外一个特征是实体类在问题域中有自己的生命周期。他们被创建，由领域事件驱动，经历各种状态的变化，最终到达一个结束的状态（即可能被删除，但是不一定是物理删除）。实体类通常涉及其他的实体，并包含值对象或者基本类型（类似Java中的int boolean double 等）

实体类 vs 值对象
一个领域概念到底是实体还是值对象？这个要看上下文。而且事实上，一个概念在不同的上下文中他的类型是不一样的。比如一个地址可能是一个商店的属性，一个值对象。但是当他们是一个客户的配送和计费地址的时候，他们就是实体类了，因为这些地址有了生命周期，可以被创建、修改、删除。

#### 聚合根
有一些实体在系统中扮演了很特殊的角色。举例，一个由订单项构成的订单，订单实体可以通过遍历内部的订单项单价，计算出一个总价，如果总价高于某个设定最小值，该订单才会生效。这种实体通常我们把它上升为聚合根，聚合根有如下的含义
1. 聚合根对聚合的整体状态负责，对聚合的状态的修改会导致聚合迁移到一个状态或者触发一个异常
2. 为了保证这个职责，仓储（Repository）只能访问聚合根。非聚合根的实体类的状态变更，必须通过聚合根触发。（译者：仓储针对聚合根和他的属性对象是整存整取，比如获取一辆车的轮子，我们Repository只能有获取车辆的方法，轮子是通过车辆的属性访问，不能直接访问轮子对象）
3. 聚合形成一个自然的一致性边界，对其他聚合的应用需要通过by-id的方式实现。

#### 仓储
从概念上讲，仓库模拟聚合根的集合，并允许访问聚合的子集或者单个属性。他们通常由某种持久化机制支撑，但是不应该把他们暴露给客户端。仓储可以引用实体，但是反过来不行。

#### 领域服务
领域服务是实现那种无法唯一的分配到实体或者值对象方法的服务，同时他还承担了实体值对象和仓储之间的功能逻辑（译者：领域服务通常承担领域对象间的方法，领域服务是无状态的，比如两个账号之间的转账）。虽然有领域服务，但是业务逻辑尽量的多在实体类和值对象中实现。因为他们可以更容易的被测试。

### 在Spring 应用中的领域驱动设计
领域的概念和DDD概念的映射方法极大的影响了这些概念到代码的映射。想要有效的编写基于Spring的Java应用，必须区分newables和injectables这两类对象。
正如名字所暗示的那样，这个区别就在开发者在获取特定领域元素的方式中。一个newable的对象是可以通过操作符实例化，当然这种实例化必须限制在尽可能少的地方，如果能使用工厂模式会比较好，实体和值对象是newable的。而injectable往往是一个spring的组件，这意味着spring将控制其生命周期，创建实例并销毁它们。这将允许Spring容器为服务的实例提供事务和安全的基础设施。客户端通过依赖注入的方式获取实例，Repository和Service都是需要注入的。

这两组内的区别定义在依赖方向从injectables到newables。 一般来说

* 值对象Value Object - JPA @Embeddable 注解 +对应的equals(…)和 hashCode()方法 （Lombok的@value可以有效的帮助生成）
* 实体类Entity - JPA @entity 注解 +对应的equals(…)和 hashCode()方法
* 仓储Repository - Spring的组件，通常用Spring的Repository的接口。
* 领域服务Domain Service - 通常是Spring的组件，使用@component注解。如果这个服务不需要事务或者安全的支撑，也可以直接new。
* 
不是Spring中所有的对象都可以映射到DDD的分类中。剩下的一般分为两组：

**应用配置Application configuration** - 对象或者配置模块
**技术适配器Technical adapter** - 用于将Spring的逻辑层通过一种远程访问技术发布给客户端。在web应用中，远程技术指的是HTTP、HTML和JavaScript。在Spring MVC中，Controller对象将负责翻译远程的对象(比如request、request parameter、payload、response等） 为本地的领域概念，并调用Service、实体类、值对象等等。

### 深入阅读
检查Guestbook和Videoshop确保您了解了哪些类实现哪些DDD的概念
区别您了解这些概念（实体类的主键、值对象的不变性、服务的依赖注入）的不同特性是如何实现的。

Appendix
Appendix A: Bibliography

* [ddd] - Eric Evans — Domain-Driven Design: Tackling Complexity in the Heart of Software- Addison Wesley. 2003.
* [ddd-quickly] - Abel Avram, Floyd Marinescu — Domain-Driven Design Quickly. InfoQ. 2006.
* [power-of-value-objects] - Dan Bergh Johnsson — Power Use of Value Objects in DDD. InfoQ. 2009.

