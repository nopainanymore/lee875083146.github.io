---
title: SpringBoot Bean初始化后的操作
tags: [SpringBoot,Bean]
categories:
- SpringBoot
- Bean
date: 2019-10-21 16:39:51
entitle: SpringBoot-Bean-initialize
---

**Unfinished**

本文介绍Spring中Bean初始化后操作的实现方式即他们之间的区别。

<!--more-->


## @PostConstruct

## InitializingBean

## ApplicationRunner

## CommandLineRunner



可以看出优先执行依然是构造方法，这个是java的语言决定的，毕竟spring只是建立在java之上的框架。然后才是被PostConstruct修饰的方法，要注意的是这个方法在对象的初始化和依赖都完成之后才会执行，所以不必担心执行这个方法的时候有个别成员属性没有被初始化为null的情况发生。在init方法之后执行的才是afterPropertiesSet方法，这个方法必须实现InitializingBean接口，这个接口很多spring的bean都实现了他，从他的方法名就能看出在属性都被设置了之后执行，也属于springbean初始化方法。


Spring 容器中的 Bean 是有生命周期的，Spring 允许在 Bean 在初始化完成后以及 Bean 销毁前执行特定的操作，常用的设定方式有以下三种：
通过实现 InitializingBean/DisposableBean 接口来定制初始化之后/销毁之前的操作方法；
通过 元素的 init-method/destroy-method属性指定初始化之后 /销毁之前调用的操作方法；
在指定方法上加上@PostConstruct 或@PreDestroy注解来制定该方法是在初始化之后还是销毁之前调用
